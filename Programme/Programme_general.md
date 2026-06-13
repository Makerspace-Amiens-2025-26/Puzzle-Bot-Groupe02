import cv2                          # OpenCV
import cv2.aruco as aruco           # Module ArUco pour la détection de marqueurs
import serial                       # Communication série avec GRBL
import time                         # Gestion des temporisations
import numpy as np                  # Calculs numériques
from collections import deque       # File FIFO utilisée pour le filtrage des positions


# CONFIG

PORT = "COM5"                       # Port série utilisé
VITESSE = 4000                      # Vitesse XY en mm/min
VITESSE_Z = 200                     # Vitesse axe Z
Z_DESCENTE = -0.9                   # Hauteur de prise/dépose

IDS_PIECES = [5, 4, 6, 7]           # IDs ArUco des pièces

# FILTRE STABILISATION

N_MOYENNE = 50                      # Taille de la moyenne glissante

historique_positions = {}           # Historique positions XY par ID
historique_angles = {}              # Historique angles par ID

angles_pieces = {}                  # Angle final stabilisé par pièce


cameraMatrix = np.load("cameraMatrix.npy")   # Calibration caméra
distCoeffs = np.load("distCoeffs.npy")       # Distorsion caméra


# HOMOGRAPHIE DAMIER

H = np.load("H_plateau.npy")       # Transformation pixel -> plateau (mm)


# ARUCO

aruco_dict = aruco.getPredefinedDictionary(1)  # Dictionnaire ArUco
parameters = aruco.DetectorParameters()        # Paramètres détecteur
cap = cv2.VideoCapture(1, cv2.CAP_DSHOW)      # Caméra


# COMMUNICATION GRBL

def envoyer_et_attendre(ser, cmd):

# Envoie une commande GRBL et attend le retour "ok"

    ser.write((cmd + "\n").encode())
    print("->", cmd)

    while True:
        line = ser.readline().decode('utf-8').strip()

        if line:
            print("<-", line)

            if "ok" in line.lower():
                break


def gerer_pompe_et_vanne(ser, action):

# Gestion aspiration / soufflage

    if action == "ASPIRER":

        print("M8 aspiration")
        envoyer_et_attendre(ser, "M8")
        time.sleep(1.5)

    elif action == "LACHER":

        print("M9 stop aspiration")
        envoyer_et_attendre(ser, "M9")
        time.sleep(0.1)

        print("M7 soufflage")
        envoyer_et_attendre(ser, "M7")
        time.sleep(1.0)

        print("M9 stop final")
        envoyer_et_attendre(ser, "M9")
        time.sleep(0.5)


def deplacer_z(ser, z):

# Déplacement axe Z + synchronisation GRBL

    envoyer_et_attendre(
        ser,
        f"G1 Z{z:.2f} F{VITESSE_Z}"
    )

    envoyer_et_attendre(ser, "G4 P0")


# ANGLE → SERVO (NOUVEAU)

def get_angle_from_aruco(corners):

# Calcul de l'orientation du marqueur ArUco

    pts = corners[0]

    p1 = pts[0]
    p2 = pts[1]

    dx = p2[0] - p1[0]
    dy = p2[1] - p1[1]

    angle = np.degrees(np.arctan2(dy, dx))

    angle = -angle   # correction orientation caméra

    return angle


def angle_to_S(angle):

# Conversion angle → commande servo GRBL

    S = 17.7778 * angle

    return int(np.clip(S, 0, 1600))


# STABILISATION ANGLE

def stabiliser_angle(angle, tid):

# Moyenne glissante pour stabiliser l'angle

    if tid not in historique_angles:
        historique_angles[tid] = deque(maxlen=N_MOYENNE)

    historique_angles[tid].append(angle)

    return np.mean(historique_angles[tid])


# VISION + SUIVI

print("Initialisation caméra...")

cibles_finales = {}        # Positions destination
positions_actuelles = {}   # Positions départ

while True:

    ret, frame = cap.read()

    frame = cv2.undistort(
        frame,
        cameraMatrix,
        distCoeffs
    )

    if not ret:
        break

    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

    corners, ids, _ = aruco.detectMarkers(
        gray,
        aruco_dict,
        parameters=parameters
    )

    pieces = {}

    if ids is not None:

        ids_l = ids.flatten().tolist()

        for tid in IDS_PIECES:

            if tid in ids_l:

                idx = ids_l.index(tid)

                center = np.mean(corners[idx][0], axis=0)

                x, y = convertir(center, tid)

                pieces[tid] = (x, y)

                # angle ArUco
                angle = get_angle_from_aruco(corners[idx])

                # stabilisation angle
                angle_stable = stabiliser_angle(angle, tid)

                angles_pieces[tid] = angle_stable

                cv2.putText(
                    frame,
                    f"ID{tid}: {x:.1f},{y:.1f} A={angle_stable:.1f}",
                    (10, 30 + tid * 20),
                    0,
                    0.6,
                    (0, 255, 0),
                    2
                )

    key = cv2.waitKey(1) & 0xFF

    if key == ord('a'):

        print("Stabilisation 2 secondes")
        time.sleep(2)

        cibles_finales = pieces.copy()

        print("Positions finales enregistrées")

    if key == ord('s'):

        print("Stabilisation 2 secondes")
        time.sleep(2)

        positions_actuelles = pieces.copy()

        print("Positions actuelles enregistrées")
        break

    cv2.imshow("VISION", frame)

    if key == ord('q'):
        break


cap.release()
cv2.destroyAllWindows()


# EXECUTION GRBL

if len(positions_actuelles) >= len(IDS_PIECES):

    ser = serial.Serial(PORT, 115200, timeout=2)

    time.sleep(2)

    envoyer_et_attendre(ser, "$X")
    envoyer_et_attendre(ser, "G21")
    envoyer_et_attendre(ser, "G90")
    envoyer_et_attendre(ser, "G92 X0 Y0 Z0")
    envoyer_et_attendre(ser, "M9")

    # servo origine
    envoyer_et_attendre(ser, "M3 S0")
    time.sleep(1)

    for tid in IDS_PIECES:

        print("\nRetour servo origine")
        envoyer_et_attendre(ser, "M3 S0")
        time.sleep(0.5)

        ax, ay = positions_actuelles[tid]
        fx, fy = cibles_finales[tid]

        print(f"\nPIECE {tid}")

        envoyer_et_attendre(
            ser,
            f"G1 X{ax:.2f} Y{-ay:.2f} F{VITESSE}"
        )

        envoyer_et_attendre(ser, "G4 P0")

        deplacer_z(ser, Z_DESCENTE)

        gerer_pompe_et_vanne(ser, "ASPIRER")

        deplacer_z(ser, 0)

        # ROTATION PIÈCE

        angle = angles_pieces[tid]

        S = angle_to_S(angle)

        print(
            f"PIECE {tid} angle={angle:.1f}° → S={S}"
        )

        envoyer_et_attendre(
            ser,
            f"M3 S{S}"
        )

        envoyer_et_attendre(
            ser,
            f"G1 X{fx:.2f} Y{-fy:.2f} F{VITESSE}"
        )

        envoyer_et_attendre(ser, "G4 P0")

        deplacer_z(ser, Z_DESCENTE)

        gerer_pompe_et_vanne(ser, "LACHER")

        deplacer_z(ser, 0)

        print("Retour servo origine")
        envoyer_et_attendre(ser, "M3 S0")
        time.sleep(0.5)

    ser.close()
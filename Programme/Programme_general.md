

import cv2                          # OpenCV
import cv2.aruco as aruco           # Module ArUco pour la détection de marqueurs
import serial                       # Communication série avec GRBL
import time                         # Gestion des temporisations
import numpy as np                  # Calculs numériques
from collections import deque       # File FIFO utilisée pour le filtrage des positions


# CONFIG

PORT = "COM5"                       # Port série utilisé

VITESSE = 4000                      # Vitesse XY en mm/min
VITESSE_Z = 200                     # Vitesse de déplacement de l'axe Z
Z_DESCENTE = -1.1                   # Hauteur de prise/dépôt de la pièce

IDS_PIECES = [5, 4, 6, 7]           # IDs ArUco des pièces à manipuler


# STABILISATION VISION

N_MOYENNE = 50                      # Nombre de prises pour en faire une moyenne
historique_positions = {}           # Historique des positions pour chaque ID

# Calibration de la caméra
cameraMatrix = np.load("cameraMatrix.npy")
distCoeffs = np.load("distCoeffs.npy")


# HOMOGRAPHIE DAMIER

# Transformation pixel -> coordonnées plateau (mm)
H = np.load("H_plateau.npy")


# ARUCO

# Chargement du dictionnaire ArUco
aruco_dict = aruco.getPredefinedDictionary(1)

# Paramètres du détecteur ArUco
parameters = aruco.DetectorParameters()

# Ouverture de la caméra
cap = cv2.VideoCapture(1, cv2.CAP_DSHOW)


# COMMUNICATION GRBL

def envoyer_et_attendre(ser, cmd):

# Envoie une commande à GRBL puis attend la réponse 'ok'

    ser.write((cmd + "\n").encode())
    print("->", cmd)

    while True:
        line = ser.readline().decode('utf-8').strip()

        if line:
            print("<-", line)

            if "ok" in line.lower():
                break


def gerer_pompe_et_vanne(ser, action):

# Gestion de l'aspiration et du soufflage
    """ASPIRER :
        M8 = aspiration

    LACHER :
        M9 = arrêt aspiration
        M7 = soufflage
        M9 = arrêt soufflage
    """

    if action == "ASPIRER":

        print("M8 aspiration")
        envoyer_et_attendre(ser, "M8")
        time.sleep(1.5)

    elif action == "LACHER":

        print("M9 stop")
        envoyer_et_attendre(ser, "M9")
        time.sleep(0.1)

        print("M7 soufflage")
        envoyer_et_attendre(ser, "M7")
        time.sleep(1.0)

        print("M9 stop final")
        envoyer_et_attendre(ser, "M9")
        time.sleep(0.5)


def deplacer_z(ser, z):

# Déplacement de l'axe Z à la position demandée

    envoyer_et_attendre(
        ser,
        f"G1 Z{z:.2f} F{VITESSE_Z}"
    )

    # Synchronisation GRBL
    envoyer_et_attendre(ser, "G4 P0")



# VISION + FILTRE STABLE

def convertir(center, tid):

# Convertit la position du marqueur
    """
    Pixels caméra
          ↓
    Homographie
          ↓
    Coordonnées plateau (mm)

    Puis applique la moyenne pour stabiliser les mesures.
    """

    # Point image
    pt = np.array(
        [[[center[0], center[1]]]],
        dtype=np.float32
    )

    # Transformation perspective
    mm = cv2.perspectiveTransform(pt, H)

    x = float(mm[0][0][0])
    y = float(mm[0][0][1])

    # Création de l'historique si nécessaire
    if tid not in historique_positions:
        historique_positions[tid] = deque(maxlen=N_MOYENNE)

    # Ajout de la mesure courante
    historique_positions[tid].append((x, y))

    # Calcul de la moyenne
    xs = [p[0] for p in historique_positions[tid]]
    ys = [p[1] for p in historique_positions[tid]]

    x = np.mean(xs)
    y = np.mean(ys)

    return x, y



# VISION POSITIONS

print("Initialisation caméra")

# Positions finales (référence)
cibles_finales = {}

# Positions actuelles des pièces
positions_actuelles = {}

while True:

    # Récupère l'image
    ret, frame = cap.read()

    # Correction de la distorsion optique (car caméra grand angle)
    frame = cv2.undistort(
        frame,
        cameraMatrix,
        distCoeffs
    )

    if not ret:
        break

    # Conversion couleurs en niveaux de gris
    gray = cv2.cvtColor(
        frame,
        cv2.COLOR_BGR2GRAY
    )

    # Détection ArUco
    corners, ids, _ = aruco.detectMarkers(
        gray,
        aruco_dict,
        parameters=parameters
    )

    pieces = {}

    if ids is not None:

        ids_l = ids.flatten().tolist()

        # Recherche des IDs attendus
        for tid in IDS_PIECES:

            if tid in ids_l:

                idx = ids_l.index(tid)

                # Centre du marqueur
                center = np.mean(
                    corners[idx][0],
                    axis=0
                )

                # Conversion en coordonnées plateau
                x, y = convertir(center, tid)

                pieces[tid] = (x, y)

                # Affichage sur l'écran
                cv2.putText(
                    frame,
                    f"ID{tid}: {x:.1f},{y:.1f}",
                    (10, 30 + tid * 20),
                    0,
                    0.6,
                    (0, 255, 0),
                    2
                )

    # Lecture clavier (commandes a et s)
    key = cv2.waitKey(1) & 0xFF

 
    # MEMOIRE POSITIONS FINALES

    if key == ord('a'):

        print("Stabilisation 2 secondes")
        time.sleep(2)

        # Sauvegarde des positions finales
        cibles_finales = pieces.copy()

        print("Positions finales enregistrées")


    # POSITIONS ACTUELLES

    if key == ord('s'):

        print("Stabilisation 2 secondes")
        time.sleep(2)

        # Sauvegarde des positions actuelles
        positions_actuelles = pieces.copy()

        print("Positions actuelles enregistrées")

        # Sortie de la boucle
        break

    # Affichage vidéo
    cv2.imshow("VISION", frame)

    # Fermeture manuelle
    if key == ord('q'):
        break

# Libération de la caméra
cap.release()
cv2.destroyAllWindows()


# EXECUTION GRBL

# Vérifie que toutes les pièces ont été détectées
if len(positions_actuelles) >= len(IDS_PIECES):

    # Connexion série à GRBL
    ser = serial.Serial(
        PORT,
        115200,
        timeout=2
    )

    time.sleep(2)

    # Déverrouillage GRBL
    envoyer_et_attendre(ser, "$X")

    # Unités en mm
    envoyer_et_attendre(ser, "G21")

    # Coordonnées absolues
    envoyer_et_attendre(ser, "G90")

    # Définition de l'origine courante
    envoyer_et_attendre(ser, "G92 X0 Y0 Z0")

    # Pompe arrêtée (sécurité)
    envoyer_et_attendre(ser, "M9")

    # Traitement pièce par pièce
    for tid in IDS_PIECES:

        ax, ay = positions_actuelles[tid]
        fx, fy = cibles_finales[tid]

        print(f"\nPIECE {tid}")


        # ALLER CHERCHER LA PIÈCE

        envoyer_et_attendre(
            ser,
            f"G1 X{ax:.2f} Y{-ay:.2f} F{VITESSE}"
        )

        envoyer_et_attendre(ser, "G4 P0")

        # Descente (axe Z avec ventouse)
        deplacer_z(ser, Z_DESCENTE)

        # Aspiration
        gerer_pompe_et_vanne(
            ser,
            "ASPIRER"
        )

        # Remontée
        deplacer_z(ser, 0)


        # EMMENER LA PIECE A SA POSITION FINALE

        envoyer_et_attendre(
            ser,
            f"G1 X{fx:.2f} Y{-fy:.2f} F{VITESSE}"
        )

        envoyer_et_attendre(ser, "G4 P0")

        # Descente
        deplacer_z(ser, Z_DESCENTE)

        # Dépose de la pièce
        gerer_pompe_et_vanne(
            ser,
            "LACHER"
        )

        # Remontée
        deplacer_z(ser, 0)

    # Fermeture de la liaison série
    ser.close()
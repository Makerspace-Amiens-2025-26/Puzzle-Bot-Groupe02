import cv2                      # OpenCV : vision + transformation perspective
import cv2.aruco as aruco       # Détection des marqueurs ArUco
import numpy as np              # Calculs matriciels
import os                       # Vérification dossier


# Affiche le dossier courant (pour vérifier le chemins des fichiers)
print(os.getcwd())

# Liste les fichiers disponibles
print(os.listdir())


# CHARGEMENT CALIBRATION CAMÉRA

cameraMatrix = np.load("cameraMatrix.npy")   # paramètres caméra
distCoeffs = np.load("distCoeffs.npy")       # correction distorsion optique


# INITIALISATION CAMÉRA

cap = cv2.VideoCapture(1, cv2.CAP_DSHOW)


# ARUCO CONFIGURATION

aruco_dict = aruco.getPredefinedDictionary(1)   # dictionnaire ArUco utilisé
parameters = aruco.DetectorParameters()         # paramètres de détection

# Liste des échantillons pour stabiliser l’homographie
samples = []


# POINTS PLATEAU

# On collecte 50 observations pour améliorer la précision
while len(samples) < 50:

    # Lecture caméra
    ret, frame = cap.read()

    # Correction distorsion optique (caméra grand angle)
    frame = cv2.undistort(
        frame,
        cameraMatrix,
        distCoeffs
    )

    # Conversion des couleurs en niveaux de gris
    gray = cv2.cvtColor(
        frame,
        cv2.COLOR_BGR2GRAY
    )

    # Détection des marqueurs ArUco
    corners, ids, _ = aruco.detectMarkers(
        gray,
        aruco_dict,
        parameters=parameters
    )

    # Si aucun marqueur détecté : on ignore
    if ids is None:
        continue

    # Conversion IDs en liste
    ids_l = ids.flatten().tolist()

    # On exige la présence des 4 marqueurs du plateau
    if not all(i in ids_l for i in [0, 1, 2, 3]):
        continue


    # CENTRE DE L'ARUCO

    def centre(i):
    # Retourne le centre (x,y) du marqueur i

        idx = ids_l.index(i)

        return np.mean(
            corners[idx][0],
            axis=0
        )

    # Récupération des 4 coins du plateau
    p0 = centre(0)
    p1 = centre(1)
    p2 = centre(2)
    p3 = centre(3)

    # Sauvegarde d'un échantillon complet
    samples.append([p0, p1, p2, p3])

    print(len(samples), "/50")

# Fermeture caméra
cap.release()


# MOYENNE DES MESURES

samples = np.array(samples)

# Moyenne des positions (pour réduire les fluctuations des coordonnées)
p0 = np.mean(samples[:, 0, :], axis=0)
p1 = np.mean(samples[:, 1, :], axis=0)
p2 = np.mean(samples[:, 2, :], axis=0)
p3 = np.mean(samples[:, 3, :], axis=0)


# CONSTRUCTION DES POINTS HOMOGRAPHIE

# Points détectés dans l’image
pts_image = np.float32([
    p0,
    p1,
    p2,
    p3
])

# Points réels sur le plateau (mm)
pts_reels = np.float32([
    [0, 0],
    [200, 0],
    [0, 300],
    [200, 300]
])


# CALCUL DE L’HOMOGRAPHIE

# Matrice qui transforme : image caméra → coordonnées plateau (mm)
H = cv2.getPerspectiveTransform(
    pts_image,
    pts_reels
)


# SAUVEGARDE

np.save(
    "H_plateau.npy",
    H
)

print("H_plateau sauvegardé")

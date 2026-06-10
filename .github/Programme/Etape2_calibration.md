import cv2                      # OpenCV : traitement image + calibration caméra
import numpy as np              # Calculs matriciels
import glob                     # Lecture de fichiers : images (*.jpg)


# PARAMÈTRES DU DAMIER

# Taille du damier (nombre de coins visibles)
CHECKERBOARD = (7, 5)


# PRÉPARATION DU MODÈLE 3D DU DAMIER

# Création des points 3D du damier dans le repère réel (avec Z=0)
objp = np.zeros(
    (CHECKERBOARD[0] * CHECKERBOARD[1], 3),
    np.float32
)

# Remplissage des coordonnées X,Y du damier
objp[:, :2] = np.mgrid[
    0:CHECKERBOARD[0],
    0:CHECKERBOARD[1]
].T.reshape(-1, 2)


# STOCKAGE DES CORRESPONDANCES

objpoints = []   # Points 3D réels
imgpoints = []   # Points 2D détectés (image caméra)


# CHARGEMENT DES IMAGES

# Récupère toutes les images de la calibration
images = glob.glob("images_calibration/*.jpg")


# DÉTECTION DU DAMIER

for fname in images:

    # Lecture image
    img = cv2.imread(fname)

    # Conversion couleurs en niveaux de gris
    gray = cv2.cvtColor(
        img,
        cv2.COLOR_BGR2GRAY
    )

    # Détection des coins du damier
    ret, corners = cv2.findChessboardCorners(
        gray,
        CHECKERBOARD,
        None
    )

    # Si damier détecté correctement
    if ret:

        # Ajout des points 3D correspondants
        objpoints.append(objp)

        # Raffinement subpixel des coins (plus précis que pixels entiers)
        corners2 = cv2.cornerSubPix(
            gray,
            corners,
            (11, 11),    
            (-1, -1),
            (
                cv2.TERM_CRITERIA_EPS
                +
                cv2.TERM_CRITERIA_MAX_ITER,
                30,       # max itérations
                0.001     # précision
            )
        )

        # Ajout des points image améliorés
        imgpoints.append(corners2)

        print("OK")   # image valide pour calibration


# CALIBRATION CAMÉRA

ret, cameraMatrix, distCoeffs, rvecs, tvecs = cv2.calibrateCamera(
    objpoints,           # points 3D
    imgpoints,           # points 2D image
    gray.shape[::-1],    # taille image (width, height)
    None,
    None
)


# SAUVEGARDE DES RÉSULTATS

np.save("cameraMatrix.npy", cameraMatrix)   # matrice intrinsèque caméra
np.save("distCoeffs.npy", distCoeffs)       # coefficients de distorsion


print("Calibration terminée")
print("Sauvegarde cameraMatrix et distCoeffs")
print("nb images:", len(images))

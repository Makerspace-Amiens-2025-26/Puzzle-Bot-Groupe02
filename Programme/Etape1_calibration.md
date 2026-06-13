import cv2                      # OpenCV : capture vidéo + traitement d'image
import os                       # Gestion des dossiers/fichiers


# CONFIGURATION

CAM_INDEX = 1          # Index de la caméra (0 = webcam principale de l'ordinateur, 1 = caméra secondaire / caméra grand angle de la machine)
NB_IMAGES = 30         # Nombre d'images à capturer pour la calibration


# INITIALISATION CAMERA

# Ouverture de la caméra
cap = cv2.VideoCapture(CAM_INDEX, cv2.CAP_DSHOW)

# Création du dossier de sauvegarde si inexistant
os.makedirs("images_calibration", exist_ok=True)

# Compteur d'images capturées
compteur = 0


# BOUCLE PRINCIPALE

# On continue tant qu'on n'a pas capturé le nombre d'images voulu
while compteur < NB_IMAGES:

    # Lecture d'une image
    ret, frame = cap.read()

    # Si erreur de capture, on saute cette frame
    if not ret:
        continue


    # AFFICHAGE DE LA PROGRESSION

    # Affiche le compteur directement sur l'image
    cv2.putText(
        frame,
        f"{compteur}/{NB_IMAGES}",     # Progression affichée
        (20, 40),                      # Position du texte sur l'écran (x, y)
        cv2.FONT_HERSHEY_SIMPLEX,      # Police
        1,                             # Taille du texte
        (0, 255, 0),                   # Couleur verte
        2                              # Épaisseur
    )

    # Affichage de la vidéo en direct
    cv2.imshow("Calibration", frame)

    # Lecture clavier (attente 1 ms)
    key = cv2.waitKey(1)


    # SAUVEGARDE IMAGE (ESPACE)

    # Si la touche ESPACE est pressée (code ASCII 32)
    if key == 32:

        # Sauvegarde de l'image dans le dossier calibration
        cv2.imwrite(
            f"images_calibration/img_{compteur}.jpg",
            frame
        )

        print("capture", compteur)

        # Incrément du compteur
        compteur += 1


    # QUITTER MANUELLEMENT

    # Si on appuie sur 'q' : arrêt du programme
    if key == ord('q'):
        break
   

# Libération de la caméra
cap.release()

# Fermeture des fenêtres OpenCV
cv2.destroyAllWindows()

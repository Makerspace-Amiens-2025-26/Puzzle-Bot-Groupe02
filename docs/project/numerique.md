---
layout: default
nav_order: 
title: Partie numérique
---

# Partie numérique : Traitement d'image et logique algorithmique

Cette section détaille la suite logicielle codée en Python, la capture spatiale et les éléments qui transforment un flux vidéo en trajectoire robotique.

## 1. Vision par ordinateur et système de coordonnées (OpenCV et ArUco)
Le script utilise la webcam du robot pour cartographier l'espace de travail.

* **Détection ArUco :** Chaque pièce du puzzle est étiquetée avec un marqueur ArUco unique. 
* **Création d'un repère spatial relatif :** * Le marqueur **ID 0** est collé sur la table et sert d'**origine absolue ($X_0=0, Y_0=0$)**.
    * Le marqueur **ID 1** est positionné à une distance connue et fixe (ex: 150 mm).
    * L'algorithme calcule en temps réel la distance en pixels entre l'ID 0 et l'ID 1. Il en déduit un **ratio conversion (pixels vers millimètres)**. Cela permet au robot de rester précis, même si la caméra est légèrement déplacée ou surélevée.
* **Calcul de l'orientation :** Pour connaître l'angle d'une pièce, le script extrait le vecteur formé par le Coin 0 (haut-gauche) et le Coin 1 (haut-droite) du marqueur de la pièce. Le code obtient l'orientation exacte de la pièce par rapport à l'horizontale du repère de la caméra, exprimée en degrés (de $-180^\circ$ à $+180^\circ$).

## 2. Logique de l'algorithme (les 3 étapes du script)

### Étape 1 : Phase d'apprentissage (mémorisation de la cible)
L'utilisateur place le puzzle parfaitement assemblé sous la caméra. Lors de l'appui sur la touche `A`, le script capture une image et stocke dans une base de données (`cibles_finales`) la position cartésienne géométrique $(X, Y)$ et l'angle d'orientation de chaque pièce (ID4, ID5...). C'est le modèle à reproduire.

### Étape 2 : Phase de scan actuel (l'état initial)
L'utilisateur mélange les pièces, les éparpille sur le plateau et leur fait subir des rotations aléatoires. Lors de l'appui sur `S`, le script effectue une "photographie" de l'état actuel et enregistre l'emplacement $(X_{actuel}, Y_{actuel})$ et l'orientation de chaque morceau disponible.

### Étape 3 : Calcul des écarts et séquence machine (le pick and place 3D)
Pour chaque pièce, le programme calcule la trajectoire à donner au robot.

1.  **Calcul de la trajectoire linéaire :** Le script donne l'ordre aux axes X/Y de se rendre aux coordonnées exactes de la pièce éparpillée : `G1 X[ax] Y[ay]`.
2.  **Calcul de la correction angulaire :** Le script soustrait l'angle actuel de l'angle mémorisé :
    $$\Delta\theta = \theta_{cible} - \theta_{actuel}$$
    Pour optimiser le mouvement et éviter que le servomoteur ne fasse un tour complet inutile sur lui-même, l'angle est normalisé entre $-180^\circ$ et $+180^\circ$.

## 3. Gestion des flux de synchronisation 
* **La synchronisation temps/mouvement :** Si Python envoyait toutes les lignes de code d'un coup, le robot essaierait de tourner la tête ou de descendre en Z pendant qu'il roule en X/Y.
* **Solution numérique :** Le script calcule le temps théorique que va prendre chaque mouvement physique en fonction de la distance et du paramètre de vitesse, puis applique une pause contrôlée (`time.sleep()`). Cela garantit que la commande "Axe Z descend" ne s'exécute que lorsque le robot est immobile au-dessus de sa cible.
* **Sécurisation par mode relatif (`G91`) :** Le robot travaille en déplacements relatifs. Après avoir déposé une pièce, il applique le calcul inverse de son trajet initial pour revenir précisément à son point d'ancrage de sécurité ($X=0, Y=0, Z=0$).
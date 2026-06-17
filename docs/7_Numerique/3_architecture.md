---
layout: default
nav_order: 3
parent: Numérique
title: Réflexion sur l'architecture logicielle 
---


# Réflexion sur l'architecture logicielle 

Une fois les bases matérielles validées (moteurs, drivers, communication série et premiers tests mécaniques), une phase importante du projet a consisté à définir l'architecture logicielle complète du système. 

L'objectif n'était pas encore de développer chaque module en détail, mais plutôt de réfléchir à la chaîne de traitement permettant de passer d'une image du puzzle à une action physique réalisée par la machine. 

Cette réflexion a permis de découper le projet en plusieurs briques indépendantes, chacune ayant un rôle précis. 

Le fonctionnement global imaginé est le suivant : 

Caméra 
   ↓ 
OpenCV 
   ↓ 
Détection ArUco 
   ↓ 
Calcul position + orientation 
   ↓ 
Algorithme de résolution du puzzle 
   ↓ 
Génération du G-code 
   ↓ 
Envoi via Serial 
   ↓ 
GRBL (Arduino) 
   ↓ 
Moteurs 
 

## 1. Acquisition de l'image (caméra) 

La caméra constitue l'un des principaux outils d'analyse du système, mais contrairement à de nombreux robots de vision, elle n'est pas utilisée en continu pendant l'exécution. 

Son rôle est limité à une phase d'acquisition au démarrage. 

Deux photographies sont réalisées : 

* une image du puzzle entièrement résolu  
* une image du puzzle dans son état initial (pièces mélangées).  

Ces deux images servent de référence pour l'ensemble de l'algorithme. 

Une fois les informations enregistrées en mémoire, la caméra n'est plus utilisée pendant la phase de résolution. Cette approche présente l’avantage de diminuer les risques d’imprécision liés à l'éclairage ou aux mouvements pendant l'exécution.  

La caméra agit donc comme un outil de mesure et de calibration au démarrage du système. 

 

## 2. Traitement de l'image (OpenCV) 

Les images récupérées par la caméra sont ensuite analysées à l'aide de OpenCV. 

**OpenCV fournit de nombreux outils permettant :**

* la lecture des flux vidéo 
* le traitement d'image en temps réel 
* la détection de formes 
* le filtrage d'images 
* la détection de marqueurs 
* le calcul de positions dans l'espace. 

Dans le cadre du Puzzle Bot, OpenCV sert principalement à interpréter ce que voit la caméra afin de transformer une image en informations exploitables par le programme. 

 

## 3. Détection des marqueurs ArUco 

L'étape suivante consiste à détecter des marqueurs ArUco présents sur la zone de travail. 

Les marqueurs ArUco sont des motifs carrés ressemblant à des QR codes simplifiés. 

**Chaque marqueur possède :**

* Un identifiant unique  
* une orientation identifiable  
* une position mesurable dans l'image. 

Grâce à ces marqueurs, le système peut connaître précisément où se trouve une pièce  et comment est orienté un élément. 

 

### 3.1 Calcul de la position et de l'orientation 

Une fois les marqueurs détectés, le programme calcule leur position dans l'espace (coordonnée X, coordonnée Y et angle de rotation). 

Le système peut ainsi déterminer : 

**Pièce 4 :** 
* X = 154 mm 
* Y = 86 mm 
* Rotation = 90° 

Ces données deviennent ensuite la référence utilisée pour tous les déplacements du robot. 

 

## 4. Algorithme de résolution du puzzle 

Une fois la position de chaque pièce connue, le robot doit être capable de déterminer les actions à effectuer. C'est le rôle de l'algorithme de résolution. Ce module constitue le "cerveau" du Puzzle Bot. 

**Ses missions sont :** 

* analyser l'état actuel du puzzle  
* identifier les déplacements nécessaires  
* éviter les mouvements inutiles. 

L'algorithme produit finalement une liste d'actions à réaliser. 

Exemple : 

Aller chercher la pièce 4 

↓ 

Prendre la pièce 

↓ 

Déplacer vers la position cible 

↓ 

Effectuer une rotation de 90° 

↓ 

Déposer la pièce 

 

## 5. Génération du G-code 

Les actions calculées par l'algorithme ne peuvent pas être exécutées directement par les moteurs, il faut d'abord les convertir dans un langage compréhensible par la machine. 

Pour cela, le robot utilise le G-code. 

Exemple : 

G0 X120 Y80 
G1 Z-1 
M3 S90 

**Chaque instruction décrit une action :** 

* déplacement des axes X et Y : pour aller chercher une pièce 
* mouvement vertical de l’axe Z : pour la prise ou le dépôt d’une pièce 
* activation du servomoteur : pour la rotation des pièces.  

 

## 6. Envoi des commandes (liaison série) 

Une fois généré, le G-code est envoyé à l'Arduino via une connexion série USB. 

La communication utilise le protocole Serial. 

Exemple : 
```
ser.write(b"G0 X120 Y80\n") 
```
Cette étape agit comme un pont entre l'intelligence du système exécutée sur l'ordinateur et la partie électronique. L'ordinateur effectue tous les calculs complexes tandis que l'Arduino se concentre sur l'exécution. 

 

## 7. GRBL 

L'Arduino contient GRBL, un firmware spécialisé dans l'interprétation du G-code. 

GRBL reçoit les commandes envoyées par l'ordinateur et se charge de : 

* synchroniser les axes  
* générer les impulsions (STEP/DIR)  
* gérer les accélérations  
* contrôler les moteurs. 

 

## 8. Déplacement physique des moteurs 

La dernière étape de la chaîne est l'exécution mécanique. 

**Une fois les commandes interprétées :** 

* les drivers reçoivent les impulsions  
* les moteurs pas-à-pas tournent  
* les axes se déplacent  
* le servomoteur effectue l'action demandée. 

 

## 9. Architecture globale 

Cette réflexion a permis de définir clairement la répartition des responsabilités : 

##### Partie vision 

Caméra 

OpenCV 

Détection ArUco 

Calcul des positions 

##### Partie intelligence 

Analyse des images 

Résolution 

Planification des actions 

Génération du G-code 

##### Partie contrôle 

Communication série 

GRBL 

Drivers moteurs 

Cette séparation permet de développer chaque partie indépendamment. 

Cette réflexion constitue la fondation du Puzzle Bot et servira de référence pour l'ensemble des développements futurs. 

 
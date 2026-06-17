---
layout: default
title: Numérique
nav_order: 7
has_children: true
has_toc: false
---



# Configuration du matériel 

Ce projet consiste à concevoir et fabriquer une machine automatisée capable de manipuler des pièces et de résoudre un puzzle. Cette documentation technique détaille la première phase critique du projet : le contrôle des moteurs (axes X, Y, Z) et du servomoteur via Arduino. 

Avant d'implémenter une logique de déplacement complexe, une phase de configuration et de sécurisation du matériel a été réalisée. 

## 1. Drivers  

Chaque moteur pas-à-pas est piloté par un driver A4988. Le réglage du courant maximal via le potentiomètre intégré est une étape indispensable. 

### 1.1 Réglage de l'intensité des drivers A4988 

**Objectifs du bridage en courant :**

* prévenir la surchauffe thermique du driver A4988 
* atténuer les vibrations du moteur pour un mouvement fluide 
* protéger l'intégrité physique des moteurs et des pistes électroniques. 

**Symptômes d'un mauvais réglage :** 

* Sous-intensité : Le moteur vibre ou fait du bruit mais ne tourne pas (perte de couple). 
* Sur-intensité : Surchauffe immédiate du driver, déclenchement de la sécurité thermique et risque de détérioration définitive du matériel. 

### 1.2 Pin EN - Enable 

Sur une CNC Shield standard, les drivers partagent une broche d'activation (EN). 

Logique conventionnelle : le driver est généralement actif à l'état bas (LOW) et désactivé à l'état haut (HIGH). Les micrologiciels comme GRBL gèrent automatiquement cette configuration. 

Particularité matérielle du projet : dans notre configuration matérielle, la logique est inversée. Le driver requiert un niveau logique haut pour s'activer. 

L'initialisation dans le code Arduino a donc été adaptée manuellement : 

```
pinMode(enablePin, OUTPUT); 
digitalWrite(enablePin, HIGH);  
 ```

## 2. Contrôle des Moteurs Pas-à-Pas (Axes X, Y, Z) 

L'objectif de cette étape est de valider le comportement des trois axes principaux (X, Y pour le plan, Z pour la verticalité). 

### 2.1 Implémentation de la bibliothèque AccelStepper 

Pour tester la fonctionnalité des moteurs, la bibliothèque AccelStepper a été choisie. Elle permet : 

* une gestion des profils d'accélération 
* un contrôle de la vitesse maximale 
* la capacité d'exécuter des mouvements multi-axes. 

### 2.2 Code de test des axes 

Le script suivant configure les vitesses et accélérations spécifiques à chaque axe et valide un mouvement de base. 

```
#include <AccelStepper.h> 
 
// Définition des broches (à adapter selon le projet) 
const int stepPinX = 2; const int dirPinX = 5; 
const int stepPinY = 3; const int dirPinY = 6; 
const int stepPinZ = 4; const int dirPinZ = 7; 
const int enablePin = 8; 
 
AccelStepper stepperX(AccelStepper::DRIVER, stepPinX, dirPinX); 
AccelStepper stepperY(AccelStepper::DRIVER, stepPinY, dirPinY); 
AccelStepper stepperZ(AccelStepper::DRIVER, stepPinZ, dirPinZ); 
 
void setup() { 
  // Gestion de la broche Enable (logique inversée) 
  pinMode(enablePin, OUTPUT); 
  digitalWrite(enablePin, HIGH);  
   
  // Configuration de l'axe X (horizontale) 
  stepperX.setMaxSpeed(1000); 
  stepperX.setAcceleration(500); 
   
  // Configuration de l'axe Y (transversale) 
  stepperY.setMaxSpeed(1000); 
  stepperY.setAcceleration(500); 
   
  // Configuration de l'axe Z (vertical) 
  stepperZ.setMaxSpeed(500); 
  stepperZ.setAcceleration(300); 
} 
 
void loop() { 
  // Commande de déplacement (absolu : vers la position 200 pas) 
  stepperX.moveTo(200); 
  stepperY.moveTo(200); 
   
  // Exécution  
  stepperX.run(); 
  stepperY.run(); 
} 
 ```

## 3. Contrôle du servomoteur 

En complément des déplacements cartésiens, un servomoteur est intégré à la tête de l'outil pour assurer la rotation des pièces du puzzle. 

### 3.1 Pilotage standard 

L'utilisation de la bibliothèque native Servo.h permet un positionnement angulaire direct. 

```
#include <Servo.h> 
 
Servo toolServo; 
 
void setup() { 
  toolServo.attach(9); // Broche de commande du servomoteur 
} 
 
void loop() { 
  toolServo.write(0);  // Position initiale 
  delay(1000); 
   
  toolServo.write(90); // Position intermédiaire 
  delay(1000); 
} 
 ```

## 4. G-code 

Pour centraliser le contrôle de la machine avec Python, un programme basé sur le G-code a été développé. 

### 4.1 Commandes de mouvement (axes X,Y,Z) 

G1 X100 Y50 : Déplacement vers les coordonnées X=100 et Y=50. 

G1 Z-1 : Descente de l'axe Z. 

## 4.2 Extension du protocole pour le servomoteur 

Pour s'intégrer au flux d'instructions des moteurs, l'actionneur du servo est piloté par des fonctions (commandes M) associées au paramètre S (représentant l'angle de 0° à 180°) : 

* M3 S0 : Position fermée (0°). 
* M3 S90 : Position intermédiaire (90°). 
* M3 S180 : Position ouverte maximale (180°). 

Cette standardisation permet d'envoyer un script simple combinant déplacement et manipulation (ex: G1 X100 Y50   G1 Z-1  M3 S180). 
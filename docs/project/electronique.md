---
layout: default
nav_order: 
title: Partie électronique 
---

# Partie électronique 

Cette section décrit la composition matérielle du robot et la nature des signaux électriques permettant de donner vie aux commandes numériques.

## 1. Choix du matériel 

* **Unité de contrôle (Microcontrôleur) :** Une carte **Arduino Uno** sert de cerveau de conversion. Elle exécute le firmware GRBL. Son rôle est de traduire les chaînes de caractères G-Code reçues par le port USB en impulsions électriques.
* **CNC Shield V3 :** Ce shield est branché directement sur l'Arduino. Il centralise les connexions, distribue l'alimentation générale et accueille les pilotes de moteurs.
* **Pilotes de moteurs (Drivers A4988 ou DRV8825) :** Ils reçoivent les signaux logiques faibles de l'Arduino (direction et impulsion) et envoient la puissance nécessaire aux bobines des moteurs pas à pas, tout en limitant le courant pour éviter la surchauffe.
* **Alimentation externe :** Elle fournit l'énergie nécessaire aux moteurs pas à pas via le CNC Shield. 

## 2. Gestion des actionneurs
Le robot utilise deux types de motorisations radicalement différentes, ce qui a nécessité une adaptation des branchements :

* **Les axes linéaires (X, Y, Z) - Moteurs pas à pas (NEMA 17) :**
    * *Principe :* Ces moteurs divisent une rotation complète en un grand nombre de "pas" (généralement 200 pas par tour, soit $1.8^\circ$ par pas).
    * *Commande :* Le CNC Shield leur envoie deux signaux numériques : **STEP** (une impulsion électrique = un pas) et **DIR** (0 ou 5V pour définir le sens de rotation antihoraire ou horaire).

* **L'axe de rotation de la tête - Servomoteur PWM :**
    * *Principe :* Le servomoteur intègre son propre capteur de position.
    * *Branchement et commande :* Il est connecté sur le CNC Shield. Il est piloté par un signal à modulation de largeur d'impulsion (**PWM** - *Pulse Width Modulation*). La carte génère un signal carré à fréquence fixe, et c'est la durée de l'impulsion qui dicte au servomoteur l'angle exact auquel il doit se positionner.

## 3. Difficultés électroniques et solutions
* **Problème de l'unique commande de broche (M3) :** Initialement, la commande `M3` gérait l'activation d'autres fonctions logiques (comme une pompe à vide). L'utilisation d'un signal partagé empêchait le servomoteur de recevoir sa consigne PWM dédiée.
* **Solution :** Isolation stricte du servomoteur de rotation sur la sortie PWM principale (Spindle) et configuration exclusive de la commande `M3 S...` pour la gestion de l'angle de la tête de prise.

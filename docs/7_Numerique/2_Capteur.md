---
layout: default
nav_order: 2
parent: Numérique
title: Ajout des capteurs 
---


# Ajout des capteurs de fin de course et gestion du homing 

Après la validation du déplacement des axes X, Y et Z, l’étape suivante consistait à intégrer des capteurs de fin de course (endstops) afin de permettre à la machine de connaître sa position de référence au démarrage. 

L'objectif était de mettre en place une procédure de homing, couramment utilisée sur les imprimantes 3D et robots cartésiens. 

 

### 1.1 Utilisation des capteurs de fin de course  

Lors de la mise sous tension, les moteurs pas-à-pas connaissent leur position relative mais ne savent pas où ils se trouvent physiquement dans l'espace. 

Les capteurs de fin de course permettent de : 

définir une origine mécanique fiable  

recalibrer automatiquement la position de la machine  

éviter les collisions avec les limites physiques  

garantir un positionnement répétable d'une session à l'autre.  

Sans procédure de homing, chaque redémarrage impose de repositionner manuellement la machine ou de supposer que sa position est connue. 

 

### 1.2 Installation des capteurs 

Les capteurs ont été ajoutés sur les axes de déplacement (X et Y). 

Principe de fonctionnement : 

l'axe se déplace lentement vers son origine 

lorsque le capteur est activé, le mouvement s'arrête 

la position courante est enregistrée comme position de référence (0,0).  

 

## 2. Homing 

La procédure de homing est exécutée au démarrage de la machine. 

Exemple simplifié : 
```
// Tant que le capteur n'est pas activé (HIGH) 

while (digitalRead(limitSwitchX) == HIGH) { 
   stepperX.move(-1); // Déplacement pas à pas vers l'origine 
   stepperX.run(); 
} 
 
stepperX.setCurrentPosition(0); 
```

Le même principe est appliqué aux autres axes. Une fois tous les deux capteurs détectés, le système dispose d'un repère absolu.  

 

## 3. Problème rencontré 

Après l'intégration des capteurs de fin de course, un comportement critique est apparu :  absence de mouvement lors du lancement des moteurs. 

Le problème est apparu immédiatement après l'ajout des endstops, ce qui a orienté les recherches vers cette partie du système. 

 

### 3.1 Analyse 

Plusieurs pistes ont été étudiées : 

erreur de câblage  

conflit logiciel avec la gestion des moteurs  

mauvais état lu sur les entrées de fin de course.  

Après plusieurs essais, nous avons conclu que l'intégration de capteurs physiques n'apportait pas de pas de bénéfice significatif pour le positionnement spatial de la machine dans le cadre de ce projet. 

 

### 3.2 Changement d'approche 

Au cours du développement, le projet a évolué vers une approche basée sur la vision par ordinateur. 

La machine utilise une caméra capable de détecter des marqueurs de référence placés sur la zone de travail. 

Cette méthode fournit directement une référence spatiale observable par la caméra, rendant les capteurs de fin de course beaucoup moins utiles dans le contexte du projet. 

 

### 3.3 Décision retenue 

Compte tenu du bug bloquant introduit par les capteurs, de la présence d'une caméra et de l'utilisation future des marqueurs référentiels, les capteurs de fin de course ont finalement été retirés. 

Le système s'appuie désormais sur la détection visuelle pour déterminer sa position. 

Même si les capteurs de fin de course n'ont pas été conservés dans la version finale, leur intégration a constitué une étape importante du développement et a contribué à orienter l'architecture vers une solution plus adaptée aux besoins du Puzzle Bot. 

 

 
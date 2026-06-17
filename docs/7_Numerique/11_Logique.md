---
layout: default
nav_order: 11
parent: Numérique
title: Logique générale de résolution 
---



# Logique générale de résolution 

Une fois les étapes de calibration terminées, le programme principal est chargé de détecter les pièces du puzzle, de calculer leurs positions réelles sur le plateau et de piloter la machine afin d'effectuer les déplacements nécessaires. 

Le fonctionnement du programme est divisé en deux grandes phases. 

## Phase 1 : Acquisition des positions 

Dans un premier temps, la caméra est utilisée pour enregistrer : 

la position finale souhaitée des pièces  

la position actuelle des pièces à déplacer. 

Ces informations sont stockées en mémoire et serviront de base à toute la résolution. 

Une fois les coordonnées enregistrées, la caméra n'est plus nécessaire. 

 

## Phase 2 : Séquence de déplacement et de rotation 

Une fois les positions enregistrées, le programme compare pour chaque pièce : 

sa position actuelle  

sa position cible  

son orientation actuelle  

son orientation attendue. 

Pour chaque pièce, le programme génère une séquence complète de type Pick-and-Place avec correction d'orientation. 

Aller chercher la pièce (X,Y) 
↓ 
Descendre l'axe Z 
↓ 
Prendre la pièce (pompe) 
↓ 
Remonter l'axe Z  
↓ 
Déplacer vers la position cible (X,Y) 
↓ 
Effectuer la rotation nécessaire (servomoteur) 
↓ 
Descendre l'axe Z 
↓ 
Déposer la pièce (électrovanne) 
↓ 
Remonter l'axe Z 
↓ 
Réinitialiser le servomoteur 
 

 
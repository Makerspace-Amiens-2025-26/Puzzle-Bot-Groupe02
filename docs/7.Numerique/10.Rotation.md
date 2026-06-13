---
layout: default
nav_order: 10
parent: Numérique
title:  Intégration de la rotation
---


# Intégration de la rotation (servomoteur) 

L’intégration du servomoteur dans un système basé sur GRBL a nécessité une approche différente de celle des moteurs pas-à-pas. 

Le servomoteur fonctionne en positions angulaires absolues, contrôlées via une commande de type M3 S.... 

L’objectif de cette étape était donc de relier des angles physiques (0°, 45°, 90°) à des valeurs de commande G-code exploitables par le système. 

 

## 1.  Contrôle du servomoteur avec GRBL 

Le servomoteur est piloté via des commandes de type : 

M3 S... 

où “S...” représente une valeur numérique interprétée comme une position. 

L’idée principale est que le servomoteur se comporte toujours de la même manière pour une même commande, indépendamment de sa position précédente. Grâce à ce fonctionnement en absolu, il n’est pas nécessaire de connaître l’état précédent du servo. 

 

### 1.1 Positions et commandes 

Afin de simplifier le comportement mécanique du système, trois positions principales et leurs commandes ont été identifiées : 

0° → M3 S400 

45° → M3 S1200 

90° → M3 S2000 

 

### 1.2 Mapping angle  

Pour transformer un angle en commande exploitable par GRBL, un mapping a été mis en place. 

Formule utilisée : 

S = 17.7778 × angle + 300 
 

Ce mapping permet de convertir directement un angle en valeur compatible avec la commande M3 S....  

Cette relation a été déterminée expérimentalement en testant différentes valeurs jusqu’à obtenir les positions mécaniques souhaitées. 

 

## 2. Remise à zéro du servomoteur 

Après chaque manipulation de pièce, une séquence de remise à zéro est effectuée. 

Le retour à S0 après chaque dépôt permet d'assurer une position de départ stable pour l’action suivante. 

 
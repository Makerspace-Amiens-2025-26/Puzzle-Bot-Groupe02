---
layout: default
nav_order: 1
parent: Electronique
title: Moteurs pas à pas
---



# Les moteurs pas à pas NEMA 17

Les déplacements du Puzzle Bot selon les axes X, Y et Z sont assurés par des moteurs pas à pas de type NEMA 17. Ce type de moteur est particulièrement adapté aux applications nécessitant un contrôle précis de la position, ce qui est essentiel pour garantir le placement correct des pièces de puzzle. Contrairement à un moteur à courant continu classique, un moteur pas à pas ne tourne pas de manière continue mais avance par incréments angulaires fixes appelés « pas ».

Chaque moteur NEMA 17 utilisé dans le système effectue généralement 200 pas pour réaliser une rotation complète, soit une résolution de 1,8° par pas. Cette caractéristique permet d'obtenir un contrôle précis des déplacements sans avoir besoin d'un capteur de position supplémentaire. En combinant ces moteurs avec les systèmes mécaniques de guidage linéaire, il devient possible de déplacer la tête de préhension avec une grande précision sur l'ensemble de la surface de travail.

Le choix des moteurs NEMA 17 résulte de leur excellent compromis entre précision, couple disponible, coût et facilité d'intégration. Ils sont largement utilisés dans les imprimantes 3D, les machines CNC et les robots de positionnement, ce qui en fait une solution éprouvée pour le Puzzle Bot.


## La commande STEP/DIR des moteurs

Le pilotage des moteurs pas à pas repose sur deux signaux numériques appelés STEP et DIR. Cette méthode de commande est largement utilisée dans les systèmes CNC car elle simplifie considérablement la gestion des déplacements.

Le signal DIR détermine le sens de rotation du moteur. Lorsqu'il est à l'état logique bas ou haut, le moteur tourne respectivement dans un sens ou dans l'autre. Le signal STEP, quant à lui, génère les mouvements. Chaque impulsion envoyée sur cette entrée correspond à un déplacement élémentaire du moteur, soit un pas ou une fraction de pas selon la configuration du pilote.

Cette méthode permet au microcontrôleur de contrôler avec précision la position, la vitesse et l'accélération de chaque axe. En ajustant simplement la fréquence des impulsions STEP, il est possible de modifier la vitesse de déplacement du robot tout en conservant une excellente précision de positionnement.
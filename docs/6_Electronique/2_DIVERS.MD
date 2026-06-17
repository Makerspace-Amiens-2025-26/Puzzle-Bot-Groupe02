---
layout: default
nav_order: 2
parent: Electronique
title: Divers
---

# Les moteurs pas à pas NEMA 17

Les déplacements du Puzzle Bot selon les axes X, Y et Z sont assurés par des moteurs pas à pas de type NEMA 17. Ce type de moteur est particulièrement adapté aux applications nécessitant un contrôle précis de la position, ce qui est essentiel pour garantir le placement correct des pièces de puzzle. Contrairement à un moteur à courant continu classique, un moteur pas à pas ne tourne pas de manière continue mais avance par incréments angulaires fixes appelés « pas ».

Chaque moteur NEMA 17 utilisé dans le système effectue généralement 200 pas pour réaliser une rotation complète, soit une résolution de 1,8° par pas. Cette caractéristique permet d'obtenir un contrôle précis des déplacements sans avoir besoin d'un capteur de position supplémentaire. En combinant ces moteurs avec les systèmes mécaniques de guidage linéaire, il devient possible de déplacer la tête de préhension avec une grande précision sur l'ensemble de la surface de travail.

Le choix des moteurs NEMA 17 résulte de leur excellent compromis entre précision, couple disponible, coût et facilité d'intégration. Ils sont largement utilisés dans les imprimantes 3D, les machines CNC et les robots de positionnement, ce qui en fait une solution éprouvée pour le Puzzle Bot.


## La commande STEP/DIR des moteurs

Le pilotage des moteurs pas à pas repose sur deux signaux numériques appelés STEP et DIR. Cette méthode de commande est largement utilisée dans les systèmes CNC car elle simplifie considérablement la gestion des déplacements.

Le signal DIR détermine le sens de rotation du moteur. Lorsqu'il est à l'état logique bas ou haut, le moteur tourne respectivement dans un sens ou dans l'autre. Le signal STEP, quant à lui, génère les mouvements. Chaque impulsion envoyée sur cette entrée correspond à un déplacement élémentaire du moteur, soit un pas ou une fraction de pas selon la configuration du pilote.

Cette méthode permet au microcontrôleur de contrôler avec précision la position, la vitesse et l'accélération de chaque axe. En ajustant simplement la fréquence des impulsions STEP, il est possible de modifier la vitesse de déplacement du robot tout en conservant une excellente précision de positionnement.


## Le servomoteur de rotation de la tête

La rotation de la tête de préhension est assurée par un servomoteur. Contrairement aux moteurs pas à pas, le servomoteur possède un système de contrôle intégré comprenant un moteur, un réducteur mécanique et un capteur de position interne.

Grâce à cette architecture, le servomoteur est capable de se positionner directement à un angle précis demandé par le système de commande. Cette fonctionnalité est particulièrement utile pour orienter la ventouse selon les besoins de manipulation des pièces de puzzle.

L'utilisation d'un servomoteur permet d'obtenir un contrôle simple et fiable de l'orientation de la tête tout en réduisant la complexité mécanique et électronique du système.


## La commande PWM du servomoteur

Le servomoteur est commandé à l'aide d'un signal PWM (Pulse Width Modulation). Ce signal est constitué d'une succession d'impulsions électriques répétées à fréquence constante.

La position du servomoteur dépend de la largeur de ces impulsions. Une impulsion courte correspond à une position angulaire donnée tandis qu'une impulsion plus longue correspond à une autre position. Le servomoteur analyse en permanence ce signal et ajuste automatiquement sa position pour atteindre l'angle demandé.

Cette méthode de commande présente l'avantage de nécessiter une seule ligne de signal tout en offrant un positionnement précis. Elle est donc particulièrement adaptée aux applications robotiques nécessitant un contrôle angulaire simple et efficace.


## La carte Arduino Uno

L'Arduino Uno constitue l'unité centrale de commande du Puzzle Bot. Son rôle est d'exécuter le programme chargé de coordonner les déplacements des axes ainsi que les différentes opérations de manipulation des pièces.

La carte est équipée d'un microcontrôleur capable de recevoir des instructions depuis un ordinateur via une liaison USB. Ces instructions sont transmises sous la forme de commandes G-Code, un langage largement utilisé dans le domaine des machines CNC et de l'impression 3D.

L'Arduino interprète ces commandes et les convertit en signaux électriques destinés aux différents actionneurs du système. Il joue ainsi le rôle de cerveau du robot en assurant la synchronisation entre les mouvements mécaniques et les opérations de préhension.


## Le firmware GRBL

Afin de transformer l'Arduino en véritable contrôleur de machine CNC, le firmware GRBL a été installé sur le microcontrôleur. Ce logiciel embarqué est spécialement conçu pour interpréter les instructions G-Code et générer les signaux de commande nécessaires aux moteurs.

GRBL prend en charge la gestion des trajectoires, des vitesses de déplacement et des accélérations. Il calcule en temps réel les impulsions STEP et DIR à envoyer aux différents axes afin de garantir des mouvements fluides et précis.

L'utilisation de GRBL permet de bénéficier d'une solution robuste et largement éprouvée tout en simplifiant considérablement le développement logiciel du projet.


## Le CNC Shield V3

Le CNC Shield V3 est une carte d'extension spécialement conçue pour les applications de commande d'axes motorisés. Elle est directement enfichée sur l'Arduino Uno et permet de centraliser l'ensemble des connexions du système.

Cette carte facilite le raccordement des moteurs, des capteurs et des alimentations sans avoir à réaliser un câblage complexe directement sur l'Arduino. Elle fournit également les connecteurs nécessaires à l'installation des pilotes de moteurs pas à pas.

Grâce au CNC Shield, l'intégration électronique du Puzzle Bot est simplifiée et l'architecture du système devient plus propre, plus fiable et plus facile à maintenir.


## Les pilotes de moteurs A4988

Les moteurs pas à pas ne peuvent pas être alimentés directement par les sorties de l'Arduino. Des modules pilotes spécialisés, tels que les A4988 ou les DRV8825, sont donc utilisés pour assurer leur commande.

Ces pilotes reçoivent les signaux STEP et DIR provenant du microcontrôleur et génèrent automatiquement les courants nécessaires dans les différentes bobines du moteur. Ils permettent également de réaliser du micro-pas, une technique qui améliore la résolution et la fluidité des déplacements.

Les pilotes intègrent également un système de limitation du courant qui protège les moteurs contre les surchauffes et améliore la fiabilité globale du système. Ils constituent ainsi un élément essentiel entre la partie logique et la partie puissance du robot.


## L'alimentation externe

L'ensemble des actionneurs du Puzzle Bot nécessite une puissance électrique bien supérieure à celle pouvant être fournie par le port USB de l'Arduino. Une alimentation externe est donc utilisée pour fournir l'énergie nécessaire au fonctionnement des moteurs et des différents équipements embarqués.

Cette alimentation est raccordée au CNC Shield qui redistribue ensuite la puissance vers les différents pilotes de moteurs. Les tensions et les courants fournis sont adaptés aux besoins des moteurs pas à pas afin de garantir des performances optimales.

L'utilisation d'une alimentation dédiée permet d'assurer un fonctionnement stable du système tout en évitant de surcharger l'Arduino. Elle constitue ainsi un élément indispensable au bon fonctionnement du Puzzle Bot et de ses différents sous-systèmes.
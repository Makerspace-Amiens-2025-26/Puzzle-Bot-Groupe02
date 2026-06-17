---
layout: default
nav_order: 3
parent: Electronique
title: Carte et firmware
---



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
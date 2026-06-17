---
layout: default
nav_order: 2
parent: Electronique
title: Servomoteur
---



## Le servomoteur de rotation de la tête

La rotation de la tête de préhension est assurée par un servomoteur. Contrairement aux moteurs pas à pas, le servomoteur possède un système de contrôle intégré comprenant un moteur, un réducteur mécanique et un capteur de position interne.

Grâce à cette architecture, le servomoteur est capable de se positionner directement à un angle précis demandé par le système de commande. Cette fonctionnalité est particulièrement utile pour orienter la ventouse selon les besoins de manipulation des pièces de puzzle.

L'utilisation d'un servomoteur permet d'obtenir un contrôle simple et fiable de l'orientation de la tête tout en réduisant la complexité mécanique et électronique du système.


## La commande PWM du servomoteur

Le servomoteur est commandé à l'aide d'un signal PWM (Pulse Width Modulation). Ce signal est constitué d'une succession d'impulsions électriques répétées à fréquence constante.

La position du servomoteur dépend de la largeur de ces impulsions. Une impulsion courte correspond à une position angulaire donnée tandis qu'une impulsion plus longue correspond à une autre position. Le servomoteur analyse en permanence ce signal et ajuste automatiquement sa position pour atteindre l'angle demandé.

Cette méthode de commande présente l'avantage de nécessiter une seule ligne de signal tout en offrant un positionnement précis. Elle est donc particulièrement adaptée aux applications robotiques nécessitant un contrôle angulaire simple et efficace.
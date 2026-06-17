---
layout: default
nav_order: 4
parent: Conception
title: Axe Z
---

# Conception de l'Axe Z

![AxeZ2](../images/Conception/AxeZ2.png)

## 1. Configuration de base de l'axe Z

L'axe Z est conçu pour supporter et guider le déplacement vertical du bloc outil. Sa structure mécanique repose sur un chariot mobile principal et une pièce maitresse fixée sur le chariot de l'Axe Y qui maintient le profilé et le moteur de l'Axe Z.

## 2. Le chariot

* Nous avons conçu une première pièce centrale à cet axe, elle contient le même système d'ancrage de la courroie que les autres axes, elle permet de fixer la pièce qui accueille le servo et la pompe et elle nous permet de tenir les galets.

* Les galets et les rondelles: Cette partie du chariot nous assure un déplacement fluide le long du profilé. Contrairement aux autres axes, celle-ci contient un chariot qui ne vient pas entourer le profilé par 2 pièces. Les gallets et les rondelles à base carré viennent alors se fixer à l'aide d'une vis qui les traverse pour venir les accrocher à la pièce principale du chariot.
La base carré des rondelles va se mettre du coté de la pièce afin que les forces mécaniques soient compensé afin d'éviter le desaxage des gallets.

* Le support servo/pompe: Cette pièce vient s'installer sur la pièce centrale du chariot par 2 vis qui nous assure que les pièces soient bien fixées entre elles et droites.
Cette pièce possède un espace pour fixer le servo moteur de l'Axe R ainsi que l'emplacement dédié à la pompe qui vient s'y glisser. La disposition de ces emplacements est très importante, elle permet à la camera de pouvoir identifier le plus grand espace possible sur le plateau.

| ![AxeY3](../images/Conception/AxeZ3.png) | ![AxeZ4](../images/Conception/AxeZ4.png) |
| :---: | :---: |
| **Ancrage de courroie** | **Fin de profilé** |


## 3. Spécification de la pièce maîtresse 

Pour éviter de concevoir un monobloc massif et complexe, l'ensemble a été segmenté en un sous-assemblage de plusieurs pièces imprimées en 3D et vissées entre elles :

* Maintien du profilé : Il assure la liaison entre le profilé et le reste de la machine. Il vient se fixer au chariot de l'Axe Y afin de permettre la mobilité de l'Axe.
De plus, il vient par 3 points d'ancrage maintenir le profilé pour garantir un parallélisme entre ce dernier et la pièce ainsi que la perpendicularité avec le plateau.

* Support Moteur : Fixé directement sur la pièce qui maintient le profilé, il est conçu spécifiquement pour accueillir le moteur pas-à-pas de cet axe. On y a ajouter des points d'ancrage sur les côtés, ce qui nous a permis de modéliser et fabriquer des pièces qui maintiennent les câbles.

* Support carte Électronique : Une extension dédiée à la fixation et à la protection de la carte électronique de commande de la pompe.

* Pièce de fin de profilé : Cette pièce vient se mettre en bas du profilé de l'Axe Z. Elle contient un léger renfoncement permettant à la pièce de s'aligner correctement avec le profilé, un trou de passage au centre pour la fixer et un trou oblong laissant à la poulie une marge afin de permettre un serrage optimal de la courroie.

| ![AxeY5](../images/Conception/AxeZ5.png) | ![AxeZ6](../images/Conception/AxeZ6.png) | ![AxeZ7](../images/Conception/AxeZ7.png) |
| :---: | :---: | :---: |
| **Support Moteur** | **Maintien du profilé** | **Support carte Électronique** |


## 4. Résultat sur l'assemblage global

Cette conception modulaire en trois pièces solidaires offre un excellent compromis technique. Elle permet d'imprimer beaucoup plus facilement les composants en 3D (en évitant des formes complexes nécessitant trop de supports d'impression) tout en offrant la possibilité de démonter ou de modifier uniquement la partie moteur ou la partie électronique lors de la maintenance. L'intégration finale regroupe proprement toute l'intelligence et le guidage vertical de l'axe Z sur un seul bloc mobile.

![AxeZ1](../images/Conception/AxeZ1.png)

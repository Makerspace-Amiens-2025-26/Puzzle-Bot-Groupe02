---
layout: default
nav_order: 4
title: Études et choix techniques
has_children: true
has_toc: false
---

# Études et choix techniques

Sur cette page, nous répertorions l'ensemble des décisions techniques et des recherches qui nous ont permis de choisir les meilleures solutions en fonction des informations à notre disposition.



## Notre démarche de décision

Tout au long de ce projet, plusieurs dilemmes techniques apparaîtront pour déterminer les pièces, les matériaux, le mode de fonctionnement ainsi que l’agencement de notre chaîne de production. 

Pour parvenir à un choix optimal et justifié, notre équipe suit rigoureusement un processus en **trois étapes** :

# Choix de la motorisation

​Pour répondre aux exigences de dynamique et de précision de la machine, le système intègre un total de cinq moteurs, répartis stratégiquement selon les contraintes de charge et de mouvement de chaque axe.

## Double motorisation synchronisée pour l'axe X​

Le déplacement longitudinal de l’axe X repose sur deux profilés aluminium parallèles. Pour actionner cet ensemble, nous avons fait le choix d’utiliser deux moteurs pas-à-pas fonctionnant de manière parfaitement synchronisée.
​La justification est simple, l'utilisation d'un seul moteur aurait entraîné un risque important de point dur ou de blocage mécanique lors des déplacements. En motorisant chaque côté, nous garantissons que les deux extrémités avancent à la même vitesse, éliminant ces problèmes.

## Motorisation simple pour l'axe Y
L'axe transversal Y est entraîné de manière classique par un unique moteur pas-à-pas. Ce dernier est dimensionné pour déplacer le chariot supportant l'axe vertical Z, l'axe de rotation, ainsi que la pompe.


## Choix d'un moteur pas-à-pas face aux contraintes de masse pour l'Axe Z

​L'action de l'axe Z se limite à une simple action : descendre pour saisir une pièce ou poser, puis remonter pour la surélever lors du transfert ou aller vers une autre pièce. Si un servomoteur classique aurait peut-être pu suffire dans certains cas, nous avons opté pour un moteur pas-à-pas.
​Pour justification, la charge embarquée sur cet axe est importante. Le chariot vertical doit supporter son propre poids, celui de la pompe, ainsi que le servomoteur de l'axe rotatif. Un servomoteur standard aurait manqué de couple et risqué de ne pas effectuer correctement l'action. Le moteur pas-à-pas nous garantit ce couple nécessaire pour déplacer cette masse sans difficulté.

## Servomoteur pour l'axe de rotation

​Pour l'axe R, nous avons implanté un servomoteur positionné horizontalement, disposé sur l'axe Z. Cette configuration permet d'obtenir un axe de rotation parallèle au plateau. Ce choix est fait pour faire pivoter les pièces saisies avant leur déplacement linéaire.


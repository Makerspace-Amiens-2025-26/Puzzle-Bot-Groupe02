---
layout: default
nav_order: 5
parent: Numérique
title: Choix du mode de coordonnées
---

# Choix du mode de coordonnées (absolu/relatif) 

## Mode absolu (G90) 

Au démarrage, la machine est configurée en mode de coordonnées absolues à l'aide de la commande G90. Chaque position transmise est exprimée par rapport à l'origine fixe du repère de travail (0,0). Par exemple, la commande G1 X100 Y50 déplace l'outil vers la position absolue (100,50). 

## Mode relatif (G91) 

En mode relatif, activé par la commande G91, chaque coordonnée correspond à un déplacement calculé à partir de la position actuelle du robot. Ainsi, la commande G1 X100 Y50 signifie : déplacer de +100 mm selon l'axe X et de +50 mm selon l'axe Y par rapport à sa position courante. 

## Choix du mode absolu 

Le système de vision par caméra fournit directement des coordonnées absolues. Par exemple, après analyse de l'image, une pièce peut être localisée en (135,82) et sa position cible en (55,210). 

L'utilisation du mode absolu permet alors d'envoyer directement les commandes : 
```
G1 X135 Y82 
puis 
G1 X55 Y210 
```
À l'inverse, en mode relatif, le programme devrait calculer avant chaque déplacement les écarts entre la position courante et la position cible. Le mode relatif implique donc des calculs supplémentaires et nécessite un suivi permanent de la position de la tête du robot. 

L’utilisation du mode absolue est plus adaptée puisque chaque ordre correspond directement à une position géométrique fournie par le système de vision. Les commandes étant indépendantes les unes des autres, le risque d'erreurs liées à l'accumulation d'imprécisions est réduit. 

Pour ces raisons, le mode absolu (G90) a été retenu pour l'ensemble du système. 
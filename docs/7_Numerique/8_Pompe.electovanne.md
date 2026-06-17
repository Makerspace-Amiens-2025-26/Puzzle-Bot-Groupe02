---
layout: default
nav_order: 8
parent: Numérique
title: Pompe et électrovanne 
---


# Pompe et électrovanne 

Pour manipuler les pièces du puzzle, la machine intègre une pompe et une électrovanne. Leur contrôle a évolué en deux étapes pour passer d'un système partagé à un pilotage totalement indépendant. 

 

## 1. Conflit entre la pompe et le servomoteur 

Au début du développement, le système faisait face à une contrainte technique : la pompe et le servomoteur ne pouvaient pas fonctionner en même temps à cause d'un conflit de ressources sur la carte de contrôle (broches partagées). 

Pour contourner ce problème, le pilotage de la pompe a été aligné sur les standards de G-code en utilisant les fonctions de gestion des fluides : 

M8 : Active la pompe (flux activé). 

M9 : Arrête la pompe (flux coupé). 

Cette astuce logicielle a permis d'isoler la pompe et de rendre l'utilisation des deux composants stable. 

 

## 2. Intégration de l'électrovanne 

Une électrovanne a été ajoutée pour évacuer l'air instantanément et relâcher les pièces de manière précise.  

Cet ajout nécessitait une nouvelle commande indépendante. L'objectif était d'utiliser la commande M7. 

Cependant, un nouvel obstacle est apparu : le firmware standard de la carte, qui tournait sous GRBL, ne gérait pas par défaut la commande M7. L’électrovanne était donc inutilisable. 

 

### 2.1 Mise à jour du firmware 

Pour lever ce blocage, il a fallu modifier l'intelligence embarquée de la machine. Le système a été migré vers une version spécifique et configurée de GRBL, modifiée pour prendre en charge la commande M7. 

 

Cette étape démontre qu'il est possible d'étendre les capacités d'une carte de contrôle standard pour l'adapter aux besoins précis du projet. 

 
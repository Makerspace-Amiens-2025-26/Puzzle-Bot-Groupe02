---
layout: default
nav_order: 9
parent: Numérique
title: Choix du firmware 
---


# Choix du firmware de contrôle (GRBL,FluidNC) 

Lors de l’intégration du servomoteur, une problématique importante est apparue concernant le firmware utilisé pour piloter la machine. 

Le système reposait initialement sur GRBL. Cependant, certaines limitations ont rapidement été identifiées, notamment dans la gestion du servomoteur. 

 

## 1. Limitation de GRBL  

Le firmware GRBL est principalement conçu pour : 

* piloter des moteurs pas-à-pas  
* interpréter du G-code  
* gérer des axes X, Y, Z. 

À l’origine, GRBL n'est pas utilisé pour contrôler un servomoteur. Cette limitation a conduit à envisager une alternative plus flexible. 

 

## 2. Passage à FluidNC (ESP32) 

Sur recommandation de Rémi, une solution alternative a été testée : FluidNC. 

FluidNC est un firmware moderne fonctionnant sur carte ESP32, offrant : 

* plus de broches configurables  
* une meilleure gestion des périphériques  
* une architecture plus flexible que GRBL  
* la possibilité d'intégrer plus facilement des servomoteurs  
* une configuration entièrement paramétrable. 

Cette solution impliquait également un changement matériel : passage de l’Arduino classique vers une carte ESP32. 

 

### 2.1 Problème rencontré (FluidNC) 

Malgré ses avantages, FluidNC a introduit un nouveau problème. 

Dans la configuration prévue, une seule broche (GPIO 8) devait contrôler l’activation de tous les moteurs. Cette broche servait de signal global ENABLE. 

Cependant, avec FluidNC il n’était pas possible d’utiliser librement la broche GPIO8. Cette limitation empêchait la configuration matérielle souhaitée. 

Malgré les avantages connus de FluidNC, son intégration dans le projet introduisait trop de contraintes matérielles. 

 

## 3. Retour à GRBL (décision finale) 

Après analyse des contraintes des deux solutions, le choix a été fait de revenir à GRBL. 

Les raisons principales sont : 

* compatibilité directe avec la CNC Shield utilisée  
* gestion simple et fiable des moteurs pas-à-pas  
* contrôle déjà fonctionnel dans les étapes précédentes 
* intégration plus simple avec le système existant. 

Cette phase nous a permis d’identifier les limites de GRBL pour des usages avancés et de tester une alternative moderne avec FluidNC. 
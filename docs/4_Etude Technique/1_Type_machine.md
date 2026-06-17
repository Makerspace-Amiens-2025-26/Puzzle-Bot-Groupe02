## 3. Étude et choix techniques

### 3.1. Choix de la typologie de la machine

Dans le cadre du développement de notre projet, le choix de l’architecture cinématique de la machine a constitué une étape clé. Après analyse des différents besoins fonctionnels, notre choix s'est porté sur une **architecture cartésienne**. 

La modélisation CAO ci-dessous (*Figure 1*) illustre le design retenu, mettant en évidence une configuration surélevée (en hauteur) afin d'optimiser l'espace de travail et l'accès aux différents composants.

![AxeR1](../images/Conception/AxeR1.png)

Plusieurs arguments techniques majeurs justifient cette décision :

* **Simplicité de programmation :** Contrairement aux systèmes de type Delta ou SCARA qui nécessitent des calculs de cinématique inverse complexes, la machine cartésienne associe directement les coordonnées géométriques (X, Y, Z) aux mouvements des moteurs. Cela simplifie considérablement le développement du firmware et le traitement du code de commande.
* **Indépendance des axes :** Le travail indépendant de chaque axe permet de dissocier totalement les mouvements. Cette configuration facilite la maintenance, le calibrage initial de la machine, ainsi que la gestion des trajectoires.
* **Modularité et rigidité :** Ce choix structurel offre une excellente stabilité mécanique, indispensable pour garantir la précision et la répétabilité des tâches exécutées sur le plateau de travail.
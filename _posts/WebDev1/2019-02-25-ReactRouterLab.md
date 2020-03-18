---
layout: post
title: Lab React et React Router
categories:
- WebDev1
- lab
author: Yoann Pigné
published: true
---

On veut écrire une nouvelle itération de notre Web app de manipulation IoT. On s'intéresse ici à l'arborescence de la Web App.

Voici la structure de l'application :

![IoT Sketch]({{ site.baseurl }}/images/router_iot_app.svg)

En reprenant l'idée du [*Lifting State Up*](https://reactjs.org/tutorial/tutorial.html#lifting-state-up)  dans la démo de React, Proposer une implémentation avec ***React*** et ***React Router*** de l'application qui stocke son état dans un composant globale et délègue l'affichage des différents éléments de la page à des composants *React* imbriqués.

On souhaite avoir des url du type : 

- `/` 
- `/temp_bureau`
- `/ventilateur`
- `/temperature_salle_A111`
- ...

On réutilisera, dans la mesure du possible, les éléments déjà développés dans les labs précédents. (certains éléments du modèle objets, le connecteur MQTT/WebSocket).

## Broker MQTT

Un broker MQTT avec de faux capteurs est disponible à l'adresse : `ec2-15-188-195-154.eu-west-3.compute.amazonaws.com` sur le port `1883` pour les sockets réseaux classiques et sur le port `8080` pour le support WebSocket.


## *Flexbox* ou *CSS Grid Layout*

Pour la mise en page de l'application, on utilisera au choix le mode de mise en page [`flexbox`](https://developer.mozilla.org/fr/docs/Web/CSS/Disposition_des_bo%C3%AEtes_flexibles_CSS/Utilisation_des_flexbox_en_CSS) ou ['CSS grid layouts'](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout), ou les deux.

## CSS modules

On utilise les [CSS modules](https://github.com/css-modules/css-modules) pour gérer les feuilles de style. 

On veillera a bien nommer les fichier comme décrit dans la [documentation de `create-react-app`](https://facebook.github.io/create-react-app/docs/adding-a-css-modules-stylesheet).

## Rendu

Comme pour les autres TP, on va forker un projet de base : <https://www-apps.univ-lehavre.fr/forge/2019-2020-m1/WEB-react-router-lab> et en proposer un *Merge Request* une fois le travail terminé. On n'oubliera pas de modifier le fichier `README.md` et de nommer correctement le *Merge Request* avec vos **nom**, **prénom** et **numéro d'étudiant**.

**Ce TP peut être fait en binôme.**


## Évaluation

[Liste des Aptitudes évaluées.](/teaching/WebDev1#react--reactrouter--redux)


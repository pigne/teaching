---
layout: post
title: React Intro
categories:
- WebDev1
- lab
author: Yoann Pigné
published: false
---



On veut réaliser une web app qui se connecte à une source de données (un Web Service) afin de recevoir des informations  sur l'état de capteurs dans un réseau.

On s'intéresse ici à l'affichage et à la représentation graphique des données de capteurs.

Voici une idée générale de la structure de l'application :

![IoT Sketch]({{ site.baseurl }}/images/router_iot_app.svg)

En reprenant l'idée du [*Lifting State Up*](https://reactjs.org/tutorial/tutorial.html#lifting-state-up)  dans la démo de React, Proposer une implémentation avec ***React*** de l'application qui stocke son état dans un composant globale et délègue l'affichage des différents éléments de la page à des composants *React* imbriqués.



## Source de données 

En théorie on devrait connecter cette WebApp à un WebService. Ici, pour simplifier, on se contente de donner l'url d'une ressource sous forme de fichier JSON dans l'`input` URL du composant en haut de page. Cette ressource correspond au nom d'un fichier que l'on dispose dans le serveur, dans le dossier `public`. Dès que le champ `input` est modifié, la nouvelle resource est téléchargé de façon asynchrone pour actualiser la liste des capteurs. 

On imagine que le format des fichiers JSON correspond au fichier de test du TP précédent. 

On aura besoin des [Hooks d'effet](https://fr.reactjs.org/docs/hooks-effect.html) pour charger de façon asynchrone le contenu. 


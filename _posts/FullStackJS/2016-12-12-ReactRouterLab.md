---
layout: post
title: Lab React Router
categories:
- FullStackJS
- lab
author: Yoann Pigné
published: true
---


On veut écrire une nouvelle itération de notre Web app de manipulation IoT. On s'intéresse ici à l'arborescence de la Web App.

Voici la structure de l'application :

![IoT Sketch]({{ site.baseurl }}/images/router_iot_app.svg)

En reprenant l'idée du *Lifting State Up*  dans la démo de React, Proposer une implémentation avec React et React Router de l'application qui stocke son état dans un composant globale et délègue l'affichage des différents éléments de la page à des sous-composants React.

On souhaite avoir des url du type : 

- `/` 
- `/temp_bureau`
- `/ventilateur`
- ...


On réutilisera les éléments déjà développés dans les labs précédents. (modèle objets, connecteur MQTT/WebSocket).


## Flexbox

Pour la mise en page de l'application, on utilisera le mode de mise en page [`flexbox`](https://developer.mozilla.org/fr/docs/Web/CSS/Disposition_des_bo%C3%AEtes_flexibles_CSS/Utilisation_des_flexbox_en_CSS).

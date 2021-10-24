---
layout: post
title: FullStack Lab
categories:
- WebDev2
- lab
author: Yoann Pigné
published: false
update: 2020-11-03
---

On cherche à développer une application Web avec des composants graphiques avancés capables d'afficher les données relatives aux ventes immobilières en France.

On se repose sur des données ouvertes : les [Demandes de Valeurs Foncières](https://www.data.gouv.fr/fr/datasets/demandes-de-valeurs-foncieres/).

On souhaite pouvoir visualiser des graphiques relatifs à ces données :

- une [série temporelle](https://en.wikipedia.org/wiki/Time_series) qui montre mois par mois l'évolutions du prix de vente moyen du mètre carré pour les ventes (mutations) de type "appartement" et "maison" sur toute la durée de l'observation (5 ans).
![série temporelle]({{ site.baseurl }}/images/2019-M2-WEB-FSLAB-Graphique1.jpeg)
- un [diagramme à barres](https://fr.wikipedia.org/wiki/Diagramme_%C3%A0_barres)  qui compte le nombre de ventes (mutations) par jour, semaine, mois, année, pour un intervalle de temps donné. L'utilisateur choisi la taille des groupes (jours, mois, année) et l'intervalle de temps (début, fin).
![série temporelle]({{ site.baseurl }}/images/2019-M2-WEB-FSLAB-Graphique2.jpeg)
- un [diagramme circulaire](https://fr.wikipedia.org/wiki/Diagramme_circulaire) montrant la répartition des ventes par [région](https://fr.wikipedia.org/wiki/R%C3%A9gion_fran%C3%A7aise).
![série temporelle]({{ site.baseurl }}/images/2019-M2-WEB-FSLAB-Graphique3.jpeg)

## Mise en oeuvre

Les graphiques sont réalisées avec [D3.js](https://d3js.org/).

On utilise le framework [API Platform](https://api-platform.com/) pour intégrer les graphiques D3.js dans une Web App faite en React qui va chercher les données via un Web Service défini grace à API Platform.


## Indications et travail a réaliser 

- Télécharger les 5 fichiers de données une seule fois pour ne pas surcharger les serveurs. 
- Bien lire la [notice](https://www.data.gouv.fr/fr/datasets/r/d573456c-76eb-4276-b91c-e6b9c89d6656) des fichiers et en particulier le tableau (section 6) qui indique le contenu des différentes colonnes de données.
- Il est inutile que le modèle objet de l'API comporte les mêmes champs que les données brutes. A vous de choisir.
- On utilisera si possible le [Fixtures Bundle](https://symfony.com/doc/current/bundles/DoctrineFixturesBundle/index.html) de Symfony pour charger les données.
- l'API est simple et ne comporte pas de jointure.
- Pas d'authentification, pas de modification, uniquement de la consultation.
- L'apparence graphique de l'application doit être soignée et l'esthétique de l'application doit correspondre à celle des graphiques.
- Comment tester une API réalisée avec API platform ?
- Comment tester du code D3.js ?


## Échéance et Évaluation

- Travail en groupes de 4 personnes.
- Le template de API Plateform étant hébergé sur github, on créera un projet sur Github à partir du template en cliquant sur ["use this template"](https://github.com/api-platform/api-platform/generate). 
- Un seul membre du groupe m'envoie un mail avec les **noms des 4 membres** et l'**url du projet**. 
- Le **16 novembre 2020**,  évaluation à l'oral, en groupes, en salle machine, pendant **20 minutes**.
- Une présentation du projet et une démonstration seront faites devant la classe en utilisant le grand écran.
- Évaluation par tout le monde (le prof et les étudiants).


## Aptitudes évaluées


- D1
  - D1.C4 : Tests
- D2
  - D2.C1 : Frontend : Visualisation de données
  - D2.C2 : API, pertinence du modèle de données
  - D2.C3 : Maîtriser l'architecture logicielle d'un projet (fichiers, classes, composants, dépendances)
- D4
  - D4.C1 : Savoir s'organiser, travailler en équipe, et planifier

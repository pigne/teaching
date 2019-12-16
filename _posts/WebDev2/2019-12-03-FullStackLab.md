---
layout: post
title: FullStack Lab
categories:
- WebDev2
- lab
author: Yoann Pigné
published: false
---

On cherche développer une application Web avec des composants graphiques avancés capables d'afficher les données relatives aux ventes immobilières en France.

On se repose sur les données ouvertes mises à disposition par le gouvernement : les [Demandes de Valeurs Foncières](https://www.data.gouv.fr/fr/datasets/demandes-de-valeurs-foncieres/).


On souhaite pouvoir visualiser des graphiques relatifs à ces données : 

- une [série temporelle](https://en.wikipedia.org/wiki/Time_series) qui montre mois par mois l'évolutions du prix de vente moyen du mètre carré pour les vente de type "appartement" et "maison" sur toute la durée de l'observation (5 ans).
- une [série temporelle](https://en.wikipedia.org/wiki/Time_series) qui compte le nombre de ventes (mutations) par jour, semaine, mois, année, pour un intervalle de temps donné.
- un [diagramme circulaire](https://fr.wikipedia.org/wiki/Diagramme_circulaire) montrant la répartition des ventes par [région](https://fr.wikipedia.org/wiki/R%C3%A9gion_fran%C3%A7aise).

## Mise en oeuvre

Les graphiques sont réalisées avec [D3.js](https://d3js.org/).

On utilise le framework [API Platform](https://api-platform.com/) pour intégrer les graphiques D3 dans une Web App faite en React qui va chercher les données via un Web Service défini grace à API Plateform.


## Indications

- Télécharger les 5 fichiers de données une seule fois pour ne pas surcharger les serveurs. 
- bien lire la [notice](https://www.data.gouv.fr/fr/datasets/r/d573456c-76eb-4276-b91c-e6b9c89d6656) des fichiers et en particulier le tableau qui indique le contenu des différentes colonnes de données. 
- Il est inutile que le modèle objet de l'API comporte les mêmes champs que les données brutes. A vous de choisir.
- On utilisera si possible le [Fixtures Bundle](https://symfony.com/doc/current/bundles/DoctrineFixturesBundle/index.html) de Symfony pour charger les données. 


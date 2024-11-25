---
layout: post
title: FullStack Lab
categories:
- WebDev2
- lab
author: Yoann Pigné
published: true
update: 2024-11-25
---

On cherche à développer une application Web avec des composants graphiques avancés capables d'afficher les données relatives impôts locaux en France.

On se repose un jeu de données ouvertes du site `data.gouv.fr` : les [Impôts locaux : fichier de recensement des éléments d'imposition à la fiscalité directe locale (REI) ](https://www.data.gouv.fr/fr/datasets/impots-locaux-fichier-de-recensement-des-elements-dimposition-a-la-fiscalite-directe-locale-rei-3/#/resources). Parmis les données disponibles, on s'intéresse à : 

-  la taxe foncière sur les propriétés non bâties (TFPNB) ;
-   la taxe foncière sur les propriétés bâties (TFPB) ;
-   la taxe d'habitation (TH) ;
-    la cotisation foncière des entreprises (CFE).    

On souhaite pouvoir visualiser des graphiques relatifs à ces données :

- une [série temporelle](https://en.wikipedia.org/wiki/Time_series) qui montre année par année l'évolutions d'un des taux d'imposition observés, pour chaque région française entre deux années données.
<img src="{{ site.baseurl }}/images/M2_LAB4-1.jpeg" alt="série temporelle" width="400px"/>
- un [nuage de points](https://fr.wikipedia.org/wiki/Nuage_de_points_(statistique))  qui montre la corrélation un taux d'imposition sélectionné et le volume collecté par les communes d'un département selectionné pour une année donnée
<img src="{{ site.baseurl }}/images/M2_LAB4-2.jpeg" alt="nuage de points" width="400px"/>
- un [diagramme circulaire](https://fr.wikipedia.org/wiki/Diagramme_circulaire) montrant la répartition des volumes collectés  par [région](https://fr.wikipedia.org/wiki/R%C3%A9gion_fran%C3%A7aise) pour un impot et une année donnés.
<img src="{{ site.baseurl }}/images/M2_LAB4-3.jpeg" alt="série temporelle" width="400px"/>

## Mise en oeuvre

Les graphiques sont réalisées avec [D3.js](https://d3js.org/).

On utilise le framework [API Platform](https://api-platform.com/) pour intégrer les graphiques D3.js dans une Web App faite en React qui va chercher les données via un Web Service défini grace à API Platform.


## Indications et travail a réaliser 

- Télécharger les fichiers de données une seule fois pour ne pas surcharger les serveurs car les fichiers sont très gros. 
- Bien lire la **notice** qui se trouve dans les archives téléchargées qui permet de trouver les colonnes intéressantes pour les graphiques.
- Le modèle de données conçu pour l'API ne doit pas correspondre au donénes d'orrigines trop complexes. On ne s'interesse qu'à quelques colonnes.
- On utilise la dernière version distribuée d'API Plateform sur github (<https://github.com/api-platform/api-platform/releases>). On préfèrera l'archive `.tar.gz` à l'archive `.zip`.
- On utilisera si possible le [Fixtures Bundle](https://symfony.com/doc/current/bundles/DoctrineFixturesBundle/index.html) de Symfony pour **charger** les données.
- l'API est simple et ne comporte pas de jointure.
- Pas d'authentification, pas de modification, uniquement de la consultation.
- L'apparence graphique de l'application doit être soignée et l'esthétique de l'application doit correspondre à celle des graphiques proposés.
- Comment **tester** une API réalisée avec API platform ?
- Comment **tester** du code D3.js ?


## Échéance et Évaluation

- Travail en groupes de 4 personnes. 
- Un seul membre du groupe m'envoie un mail avec les **noms des 4 membres** et l'**url du projet**. 
- Le **lundi 6 janvier 2025**,  évaluation à l'oral, en groupes, en salle machine, pendant **30 minutes**.
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

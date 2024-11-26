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

On cherche à développer une application Web avec des composants graphiques avancés capables d'afficher les données relatives aux impôts locaux en France.

On s'appuie sur un jeu de données ouvertes du site `data.gouv.fr` : les [Impôts locaux : fichier de recensement des éléments d'imposition à la fiscalité directe locale (REI)](https://www.data.gouv.fr/fr/datasets/impots-locaux-fichier-de-recensement-des-elements-dimposition-a-la-fiscalite-directe-locale-rei-3/#/resources). Parmi les données disponibles, on s'intéresse à : 

- la taxe foncière sur les propriétés non bâties (TFPNB) ;
- la taxe foncière sur les propriétés bâties (TFPB) ;
- la taxe d'habitation (TH) ;
- la cotisation foncière des entreprises (CFE).    

On souhaite pouvoir visualiser des graphiques relatifs à ces données :

- une [série temporelle](https://en.wikipedia.org/wiki/Time_series) qui montre, année par année, l'évolution d'un des taux d'imposition observés pour chaque région française entre deux années données.  
<img src="{{ site.baseurl }}/images/M2_LAB4-1.jpeg" alt="série temporelle" width="400px"/>

- un [nuage de points](https://fr.wikipedia.org/wiki/Nuage_de_points_(statistique)) qui montre la corrélation entre un taux d'imposition sélectionné et le volume collecté par les communes d'un département sélectionné pour une année donnée.  
<img src="{{ site.baseurl }}/images/M2_LAB4-2.jpeg" alt="nuage de points" width="400px"/>

- un [diagramme circulaire](https://fr.wikipedia.org/wiki/Diagramme_circulaire) montrant la répartition des volumes collectés par [région](https://fr.wikipedia.org/wiki/R%C3%A9gion_fran%C3%A7aise) pour un impôt et une année donnés.  
<img src="{{ site.baseurl }}/images/M2_LAB4-3.jpeg" alt="diagramme circulaire" width="400px"/>

## Mise en œuvre

Les graphiques sont réalisés avec [D3.js](https://d3js.org/).

On utilise le framework [API Platform](https://api-platform.com/) pour intégrer les graphiques D3.js dans une Web App développée en React, qui va chercher les données via un Web Service défini grâce à API Platform.

**Un critère clé pour ce genre de projet** :   L'application doit limiter au maximum les données échangées. Les requêtes côté serveur doivent être optimisées pour ne transmettre que l'essentiel. La quantité de données transférées, visible dans l'onglet "réseau" des outils de développement, sera un critère d'évaluation important.

## Indications et travail à réaliser 

- Télécharger les fichiers de données une seule fois pour ne pas surcharger les serveurs, car ces fichiers sont très volumineux.
- Bien lire la **notice** incluse dans les archives téléchargées pour identifier les colonnes pertinentes pour les graphiques.
- Le modèle de données conçu pour l'API doit être simplifié : il ne doit pas refléter la complexité des données d'origine. On se concentre uniquement sur les colonnes utiles.
- Utiliser la dernière version distribuée d'API Platform disponible sur GitHub (<https://github.com/api-platform/api-platform/releases>), en privilégiant l'archive `.tar.gz` plutôt que `.zip`.
- Si possible, utiliser le [Fixtures Bundle](https://symfony.com/doc/current/bundles/DoctrineFixturesBundle/index.html) de Symfony pour **charger** les données.
- L'API est simple et ne comporte pas de jointures.
- Pas d'authentification ni de modification : uniquement de la consultation.
- L'apparence graphique de l'application doit être soignée et correspondre à celle des graphiques proposés.
- Répondre aux questions suivantes : 
  - Comment **tester** une API réalisée avec API Platform ? 
  - Comment **tester** du code D3.js ?




## Échéance et évaluation

- Travail en groupes de 4 personnes. 
- Un seul membre du groupe m'envoie un mail avec les **noms des 4 membres**, l'**URL du projet** et le  titre “[M2 IWOCS WEB] Projet n°3” avant le **vendredi 6 décembre 2024**.
- Le **lundi 6 janvier 2025**, évaluation à l'oral, en groupes, pendant **30 minutes**.
- Une présentation du projet et une démonstration seront réalisées devant la classe entière.
- Évaluation par tous (professeur et étudiants).


## Aptitudes évaluées

- **D1**
  - D1.C4 : Tests
- **D2**
  - D2.C1 : Frontend : visualisation de données
  - D2.C2 : API, pertinence du modèle de données
  - D2.C3 : Maîtriser l'architecture logicielle d'un projet (fichiers, classes, composants, dépendances)
- **D4**
  - D4.C1 : Savoir s'organiser, travailler en équipe, et planifier

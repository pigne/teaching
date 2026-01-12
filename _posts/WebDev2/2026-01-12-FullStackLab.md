---
layout: post
title: FullStack Lab
categories:
- WebDev2
- lab
author: Yoann Pigné
published: true
---


On cherche à développer une application Web comportant des **composants de visualisation dynamique** permettant d’explorer les données relatives aux impôts locaux en France.

On s’appuie sur un jeu de données ouvertes de `data.gouv.fr` :  
**Impôts locaux : fichier de recensement des éléments d’imposition à la fiscalité directe locale (REI)**  
https://www.data.gouv.fr/fr/datasets/impots-locaux-fichier-de-recensement-des-elements-dimposition-a-la-fiscalite-directe-locale-rei-3/

Parmi les données disponibles, on s’intéresse à :

- la taxe foncière sur les propriétés non bâties (TFPNB)  
- la taxe foncière sur les propriétés bâties (TFPB)  
- la taxe d’habitation (TH)  
- la cotisation foncière des entreprises (CFE)  

---

## Objectifs de visualisation

L’application devra proposer plusieurs **points de vue graphiques** sur ces données.

### 1. Série temporelle

Une série temporelle montrant, année par année, l’évolution d’un taux d’imposition sélectionné pour chaque région française entre deux années données.

<img src="{{ site.baseurl }}/images/M2_LAB4-1.jpeg" width="400px"/>

---

### 2. Nuage de points

Un nuage de points montrant la corrélation entre :

- un taux d’imposition sélectionné
- le volume collecté

pour les communes d’un département donné, sur une année donnée.

<img src="{{ site.baseurl }}/images/M2_LAB4-2.jpeg" width="400px"/>

---

### 3. Diagramme circulaire

Un diagramme circulaire représentant la répartition des volumes collectés par région, pour un impôt et une année donnés.

<img src="{{ site.baseurl }}/images/M2_LAB4-3.jpeg" width="400px"/>

---

## Cadre architectural pour les graphiques

Le projet doit être conçu explicitement selon le **pipeline de la visualisation** :

```

Données → Transformation → Mise en page → Encodage → Rendu → Interaction

```

- **Données** : extraites via une API Web
- **Transformation** : filtrage, agrégation, calculs
- **Mise en page** : calcul des positions, échelles
- **Encodage** : couleur, taille, forme
- **Rendu** : SVG, Canvas ou WebGL
- **Interaction** : sélection, survol, filtres

Les choix technologiques doivent être justifiés à partir de ce pipeline.

---

## Mise en œuvre

L’application est construite comme une **Web App React** qui consomme une **API REST** fournie par **API Platform**.

API Platform est utilisé pour :
- exposer les données
- effectuer les agrégations côté serveur
- réduire le volume de données échangées

Les composants graphiques peuvent être implémentés avec une/des bibliothèques de visualisation (D3, Vega, Chart.js, etc.) dont le backend de rendu pourra être :
- SVG
- Canvas
- WebGL

Le moteur de rendu est laissé au choix, mais il doit être **cohérent avec le volume de données et les interactions demandées**.

---

## Contrainte majeure : maîtrise du flux de données

L’application doit **minimiser le volume de données transférées** entre le serveur et le client.

Les requêtes API doivent être conçues pour :
- agréger les données
- filtrer côté serveur
- ne renvoyer que ce qui est nécessaire aux visualisations

Le volume de données échangées (onglet *Network* des outils de développement) fera partie de l’évaluation.

---

## Travail demandé

### 1. Données

- Télécharger les fichiers une seule fois (volumineux)
- Lire attentivement la notice fournie
- Identifier les colonnes pertinentes
- Construire un modèle de données simplifié pour l’API

---

### 2. API

- Installer la dernière version stable d’API Platform  
  https://github.com/api-platform/api-platform/releases  
  (préférer l’archive `.tar.gz`)
- Charger les données via le **Doctrine Fixtures Bundle**
- L’API :
  - ne comporte pas de jointures complexes
  - est en lecture seule
  - expose des ressources adaptées aux besoins des graphiques

---

### 3. Visualisation

Pour chaque graphique, expliciter :

- quelles données sont demandées à l’API
- quelles transformations sont faites côté serveur
- quelles transformations sont faites côté client
- quel moteur de rendu est utilisé (SVG, Canvas, WebGL)

---

### 4. Questions à traiter dans la présentation

- Comment tester une API réalisée avec API Platform ?
- Comment tester une chaîne de visualisation (données → rendu) ?
- Quels compromis avez-vous faits entre précision, volume de données et performance ?

---


### Échéance et évaluation

- Travail en groupes de 4 personnes. 
- Un seul membre du groupe m'envoie un mail avec les **noms des 4 membres**, l'**URL du projet** et le  titre “[M2 IWOCS WEB] Projet n°3” **rapidement**.
- Le **lundi 26 janvier 2026**, évaluation à l'oral, en groupes, pendant **30 minutes**.
- Une présentation du projet et une démonstration seront réalisées devant la classe entière.
- Évaluation par tous (professeur et étudiants).


### Aptitudes évaluées

- **D1**
  - D1.C4 : Tests
- **D2**
  - D2.C1 : Frontend : visualisation de données
  - D2.C2 : API, pertinence du modèle de données
  - D2.C3 : Maîtriser l'architecture logicielle d'un projet (fichiers, classes, composants, dépendances)
- **D4**
  - D4.C1 : Savoir s'organiser, travailler en équipe, et planifier

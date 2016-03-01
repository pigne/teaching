---
layout: post
title: TP Symfony - Gestion des modèles de données
categories:  
- InfoWeb
- lab
published: true
author: Yoann Pigné
---

Ce TP est une mise en application du [cours](http://pigne.org/teaching/infoweb/lecture/Symfony-Modeles) présenté en classe. Le but principal est la **création de modèles de données persistants** à l'aide de **Doctrine**.


## Les données


On souhaite réaliser une application Web permettant de lister, localiser et donner des commentaires sur des musées Parisiens.

On dispose d'un jeu de données sous [licence ouverte (Etalab)](https://www.etalab.gouv.fr/licence-ouverte-open-licence) contenant la liste des musées parisiens labelisés "Musées de France".

Les données sont accessibles sur le  site de [OpenData de Paris](http://opendata.paris.fr/), dans plusieurs formats :

 - [Liste Musées de France à Paris](http://opendata.paris.fr/explore/dataset/liste-musees-de-france-a-paris/export/)


Le plus simple est de travailler avec le format [CSV](https://fr.wikipedia.org/wiki/Comma-separated_values) en particulier parce que PhpMyAdmin permet d'importer facilement ce format.

En CSV, la première ligne représente le **nom des colonnes** du fichier. On peut donc changer facilement les noms sur cette première ligne pour les faire correspondre à des **noms de colonnes d'une table** de base de données.

Dans l'import de  PhpMyAdmin il faut spécifier les options suivantes :

- sélectionner le format CSV
- spécifier le séparateur de champs (_Columns separated with_) : `;`
- cocher la case disant que la première ligne des données représente les noms des colonnes (_The first line of the file contains the table column names_)
- laisser les autres valeurs de paramètres par défaut.


Le nom de la table et les noms des champs peuvent être modifiées après l'import.


## Nouveau projet Symfony et configuration

Reprendre les étapes du [tp précédent](http://pigne.org/teaching/infoweb/lab/TP_Introduction_Symfony) pour créer un nouveau projet spécialement pour cette nouvelle app.

Configurer le nouveau projet pour utiliser une base de données. Il est conseillé d'utiliser MySQL pour simplifier l'import des données CSV, mais tout autre Base de données supportée par Doctrine est acceptée.

les fichiers à modifier sont :

- `app/config/parameters.yml`
- `app/config/config.yml`

## Création et persistance d'une entité

On souhaite créer une entité  principale pour représenter les musées.

Utiliser le script de création d’entités de Doctrine pour créer une entité `AppBundle:Musee` :

```bash
php bin/console doctrine:generate:entity
```

La configuration va prendre du temps car il faut créer un champ pour chaque colonne de la table que l'on a importé.

**Rappel** : le nom des champs n'est pas obligatoirement le même que les noms des colonnes dans la table. On peut spécifier le nom des colonnes dans les annotations des champs de la classe `Musee`. Il en va de même pour la concordance du nom de la table et du nom de la classe.


On vérifie la cohérence de cette classe et de ses annotations avec la table que l'on a déjà insérée dans la base :

```bash
php bin/console doctrine:schema:update --dump-sql
```

Normalement seul le champ `id` est ajouté. Les types des autres champs sont aussi modifiés.  

```sql
ALTER TABLE musee ADD id INT AUTO_INCREMENT NOT NULL,
CHANGE region region VARCHAR(25) NOT NULL,
CHANGE departement departement VARCHAR(255) NOT NULL,
CHANGE ferme ferme VARCHAR(3) NOT NULL,
CHANGE annee_reouverture annee_reouverture VARCHAR(30) NOT NULL,
CHANGE annexe annexe VARCHAR(50) NOT NULL,
CHANGE nom nom VARCHAR(255) NOT NULL,
CHANGE adresse adresse VARCHAR(255) NOT NULL,
CHANGE code_postal code_postal VARCHAR(5) NOT NULL,
CHANGE ville ville VARCHAR(50) NOT NULL,
CHANGE site_web site_web VARCHAR(255) NOT NULL,
CHANGE fermeture_annuelle fermeture_annuelle LONGTEXT NOT NULL,
CHANGE periodes_ouverture periodes_ouverture LONGTEXT NOT NULL,
CHANGE jours_nocturnes jours_nocturnes LONGTEXT NOT NULL,
CHANGE coordonnees coordonnees VARCHAR(50) NOT NULL,
ADD PRIMARY KEY (id);
```

Si tout est bon, on valide :


```bash
php bin/console doctrine:schema:update --force
```




## Consultation des musées

On souhaite pouvoir afficher la liste  tabulée des musées ainsi que la possibilité d'afficher un seul musée sur une page séparée.

L'affichage de la liste de musée ne doit pas montrer toutes les informations. De plus il y a beaucoup de données,
on doit donc proposer un affichage qui soit simple et facile à lire pour l'utilisateur.

On va dont prévoir deux  modes d'affichage :

- un mode complet où tous les musées apparaissent sans ordre,  mais ils sont paginés  (affichés pas groupes de 10) ;
- un mode d'affichage par arrondissement.


Pour réaliser des affichages on doit bien sur s'assurer que :

- l'entité `Musee` est associée à un contrôleur qui contient des actions ;
- chaque action est liées a une route ;
- chaque action est aussi associée à une vue (TWIG).
- les requêtes complexes sont "stockées" dans le _repository_ de la classe `Musee`.


## Commentaires sur les musées

Lors de l'affichage d'un musée dans sa page séparée, il serait intéressant pour les visiteurs du site de pouvoir déposer un commentaire à propos du musée en question. Il serait aussi intéressant que ces commentaires apparaissent sur cette même page.

On ne s'inquiète pas pour l'instant de la notion d'utilisateur ni de droits. Tout le monde peut commenter.

Un commentaire peut prendre différentes formes mais il doit au moins contenir :

- un nom d'auteur (champs de texte libre),
- la date de création du commentaire (générée automatiquement)
- une note de 1 à 5,
- le texte du commentaire.


Créer une classe **persistante** `Commentaire` liée avec une relation un vers plusieurs  à la classe `Musee`.
**Attention** au sens de la relation. Un musée peut avoir plusieurs commentaires et une commentaire ne concerne qu'un seul musée.

Pour les musées ayant reçu des commentaires, on souhaite stocker une note moyenne ainsi que le nombre de votes dans la classe `Musee` afin de l'afficher sur la page du musée.


## Localisation

Une application Web digne de ce nom se doit d'avoir une carte interactive...  

La plupart des services de cartographie sont accessible en javascript. Or nos données sont en base de données et on ne peut y accéder que via PHP.

N'oublions pas que PHP et surtout TWIG sont capable de générer bien plus que des pages web. Il est tout a fait possible de générer du javascript.

Prenons l'exemple de la bibliothèque [Leaflet](http://leafletjs.com). Le code suivant permet de créer une carte et d'y ajouter des _markers_.

En utilisant par exemple les blocs `stylesheets` et `javascript` par défaut du fichier  TWIG  `base.twig.html` il suffit d'ajouter la bibliothèque en référence dans la page web :

```html
<link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.css" />
<script src="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.js"></script>
```

puis de définir un élément du markup qui recevra la carte :

```html
<div id="map" style="height: 500px;"></div>
```

enfin le script suivant fera le reste. On Note  qu'il suffit de modifier le tableau `data` (avec `TWIG` par exemple) pour afficher d'avantage de  points.

```javascript
<script>
function draw_map(data) {  
  var map = L.map('map');
  var osmUrl='http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png';
  var osmAttrib='Map data © <a href="http://openstreetmap.org">OpenStreetMap</a> contributors';
  var osm = new L.TileLayer(osmUrl, {minZoom: 8, maxZoom: 14, attribution: osmAttrib});		
  map.setView(new L.LatLng(48.86, 2.34),12);
  map.addLayer(osm);
  var marker;
  data.forEach(function(musee){
    marker = L.marker([musee.lat, musee.lon]).addTo(map);
    marker.bindPopup("<b>"+musee.nom+"</b>").openPopup();
  });
  marker.openPopup();
}

var data = [
  {
    nom:'Musée Carnavalet-Histoire de Paris',
    lat:'48.85699',
    lon:'2.36285648'
  },
  {
    nom:'Maison de Balzac',
    lat:'48.85538',
    lon:'2.280755'
  },
];

draw_map(data);

</script>
```

Resultat :


<link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.css" />
<script src="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.js"></script>
<div id="map" style="height: 500px;"></div>
<script>
function draw_map(data) {  
  var map = L.map('map');
  var osmUrl='http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png';
  var osmAttrib='Map data © <a href="http://openstreetmap.org">OpenStreetMap</a> contributors';
  var osm = new L.TileLayer(osmUrl, {minZoom: 8, maxZoom: 14, attribution: osmAttrib});		
  map.setView(new L.LatLng(48.86, 2.34),12);
  map.addLayer(osm);

  data.forEach(function(musee){
    var marker = L.marker([musee.lat, musee.lon]).addTo(map);
    marker.bindPopup("<b>"+musee.nom+"</b>").openPopup();
  });
}

var data = [
  {
    nom:'Musée Carnavalet-Histoire de Paris',
    lat:'48.85699',
    lon:'2.36285648'
  },
  {
    nom:'Maison de Balzac',
    lat:'48.85538',
    lon:'2.280755'
  },
];

draw_map(data);

</script>

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


On souhaite réaliser une application Web permettant de lister, localiser et donner des commentaires sur des musées Parisiens.



## Nouveau projet Symfony et configuration

Reprendre les étapes du [tp précédent](http://pigne.org/teaching/infoweb/lab/TP_Introduction_Symfony) pour créer un nouveau projet spécialement pour cette nouvelle app.

Configurer le nouveau projet pour utiliser une base de données supportée par Doctrine.
<!-- Il est conseillé d'utiliser MySQL pour simplifier l'import des données CSV, mais tout autre Base de données supportée par Doctrine est acceptée. -->

les fichiers à modifier sont :

- `app/config/parameters.yml`
- `app/config/config.yml`

## Création et persistance d'une entité

On souhaite créer une entité  principale pour représenter les musées. après mûre réflexion on décide d'appeler ce modèle `Musee`.

Le Schémas est le suivant. Les champs sont presque tous **facultatifs** à part le **nom** et les **coordonnées** qui sont **obligatoires**.

![Modèle UML Musée]({{ site.baseurl }}/images/UML-musee-infoweb.svg)

L'énumération status peut être simplement réalisée en notant la propriété comme `string` et en définissant des constantes dans la classe `Musee`.

Utiliser le script de création d’entités de Doctrine pour créer une entité `AppBundle:Musee` :

```bash
php bin/console doctrine:generate:entity
```

La configuration va prendre du temps car il faut créer un champ pour chaque colonne de la table que l'on a importé.

**Rappel** : le nom des champs n'est pas obligatoirement le même que les noms des colonnes dans la table. On peut spécifier le nom des colonnes dans les annotations des champs de la classe `Musee`. Il en va de même pour la concordance du nom de la table et du nom de la classe.


On vérifie la cohérence de cette classe et de ses annotations avec le modèle UML :

```bash
php bin/console doctrine:schema:update --dump-sql
```

```sql
CREATE TABLE musee (
  id INT AUTO_INCREMENT NOT NULL,
  nom VARCHAR(100) NOT NULL,
  adresse VARCHAR(255) DEFAULT NULL,
  ...
  PRIMARY KEY(id)
)
DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci ENGINE = InnoDB;
```

Si tout est bon, on valide avec :


```bash
php bin/console doctrine:schema:update --force
```


## Les données


On dispose d'un jeu de données sous [licence ouverte (Etalab)](https://www.etalab.gouv.fr/licence-ouverte-open-licence) contenant la liste des musées parisiens ayant le label "Musées de France".

Les données sont accessibles sur le  site de [OpenData de Paris](http://opendata.paris.fr/), dans plusieurs formats :

 - [Liste Musées de France à Paris](http://opendata.paris.fr/explore/dataset/liste-musees-de-france-a-paris/export/)


Il y a 2 méthodes possibles pour récupérer les données et les insérer dans notre basse de données :

- Méthode 1 : la _bonne_ méthode.
- Méthode 2 :  la _mauvaise_ méthode.

A vous de choisir... **Mais n'en choisissez qu'une, ne faites pas les 2**.


### La _mauvaise_ méthode

La _mauvaise_ méthode va vous permettre d'arriver rapidement a un résultat mais il faudra jouer avec PhpMyAdmin pour que cela fonctionne. Le gros inconvénient de la méthode est qu'elle n'est pas automatique. Il faudra tout recommencer en cas de réécriture, de modification  ou de migration de la basse de données...

Pour cette méthode il faut utiliser MySQL et PhpMyAdmin. Elle consiste a télécharger les données au format [CSV](https://fr.wikipedia.org/wiki/Comma-separated_values) car PhpMyAdmin permet d'importer facilement ce format.

En CSV, la première ligne représente le **nom des colonnes** du fichier. On peut donc changer facilement les noms sur cette première ligne pour les faire correspondre à des **noms de colonnes d'une table** de base de données.

Dans l'onglet **import** de  PhpMyAdmin il faut spécifier les options suivantes :

- sélectionner le format CSV
- spécifier le séparateur de champs (_Columns separated with_) : `;`
- cocher la case disant que la première ligne des données représente les noms des colonnes (_The first line of the file contains the table column names_)
- **laisser toutes les autres valeurs de paramètres à leur valeur par défaut**.


Le nom de la table et les noms des champs peuvent être modifiées après l'import.



### La _bonne_ méthode

La _bonne_ méthode va vous permettre d'automatiser le processus d'import. De le ré-exécuter avec une simple ligne de commande. De ne pas dépendre de PhpMyAdmin ou de MySQL. Vous pouvez utiliser n'importe quelle base de données.

L'idée est d'écrire un script PHP qui va se charger de l'import.

Pour cela on a besoin d'une nouvelle dépendance dans le projet. A la racine du projet, taper la commande suivante (si `composer.phar` n'existe pas dans votre projet, copiez le depuis le dossier du TP 1 où on l'avait téléchargé, souvenez-vous) :

```bash
php composer.phar require --dev doctrine/doctrine-fixtures-bundle
```

Il faut ensuite modifier la configuration de notre app pour prendre en charge le _bundle_. Dans `app/KernelApp.php` il faut ajouter la ligne `$bundles[] = new Doctrine\Bundle\Fixtu resBundle\DoctrineFixturesBundle();` :

```php
<?php

use Symfony\Component\HttpKernel\Kernel;
use Symfony\Component\Config\Loader\LoaderInterface;

class AppKernel extends Kernel
{
    public function registerBundles()
    {
        $bundles = [
            new Symfony\Bundle\FrameworkBundle\FrameworkBundle(),
            // ...

        ];

        if (in_array($this->getEnvironment(), ['dev', 'test'], true)) {
            // ...
            $bundles[] = new Sensio\Bundle\GeneratorBundle\SensioGeneratorBundle();
            // ----------------------------
            //     INSERER LA LIGNE ICI
            $bundles[] = new Doctrine\Bundle\FixturesBundle\DoctrineFixturesBundle();
            // ----------------------------

        }
// ...        
```



On va créer une classe chargée de l'import des données dans un nouveau dossier à créer : `src/AppBundle/DataFixtures/ORM`. Cette classe (`LoadMuseeData`) contient une méthode `load` qui va être appelé pour l'import.

Dans cette méthode on va se servir des données au format [JSON](https://fr.wikipedia.org/wiki/JavaScript_Object_Notation). On observe le  document [téléchargé](http://opendata.paris.fr/explore/dataset/liste-musees-de-france-a-paris/export/) sur le site de  l'Open Data de Paris, pour en comprendre la structure :

```json
[{
  "datasetid": "liste-musees-de-france-a-paris",
  "recordid": "ee62a66f758e76fd1c4e3e1b6f8bd811bd3de9b8",
  "fields": {
    "periode_ouverture": "Ouvert de 10h \u00e0 18h du mardi au dimanche",
    "nom_du_musee": "Maison de Victor Hugo",
    "adr": "6, Place des Vosges",
    "ville": "PARIS",
    "nomreg": "ILE-DE-FRANCE",
    "sitweb": "www.musee-hugo.paris.fr",
    "fermeture_annuelle": "Jours f\u00e9ri\u00e9s",
    "coordonnees_": [48.854821, 2.366126],
    "ferme": "NON",
    "cp": 75004,
    "nomdep": "PARIS"
  },
  "geometry": {
    "type": "Point",
    "coordinates": [2.366126, 48.854821]
  },
  "record_timestamp": "2015-02-26T15:17:55+01:00"
},
{
  "datasetid": "liste-musees-de-france-a-paris",
  "recordid": "9e1b8a250e04bf2e0f51dacc2157597bea134ab7",
  "fields": {
  // ...
  }
}]
```

La méthode load va donc récupérer les informations utiles dans le fichier précédent pour créer des objet Musee et les stocker (les **persister**) dans la base de donnée:


```php
<?php
namespace AppBundle\DataFixtures\ORM;

use Doctrine\Common\DataFixtures\FixtureInterface;
use Doctrine\Common\Persistence\ObjectManager;
use AppBundle\Entity\Musee;

class LoadMuseeData implements FixtureInterface
{
  public function load(ObjectManager $manager)
  {
    $url="http://opendata.paris.fr/explore/dataset/liste-musees-de-france-a-paris/download/?format=json&timezone=Europe/Berlin";
    $contents = file_get_contents($url);
    $contents = utf8_encode($contents);
    $json = json_decode($contents, true);
    foreach ($json as $object)
    {
        $fields = $object['fields'];
        if (isset($fields['coordonnees_']))
        {
            $musee = new Musee();
            $musee->setNom($fields['nom_du_musee']);
            // ...
            $manager->persist($musee);
        }
    }
    $manager->flush();
  }
}
```


Exécuter la commande suivante et modifier le script jusqu'à ce que l'import fonctionne et que toutes les données nécessaires dans le modèle soient considérées:

```bash
php bin/console doctrine:fixtures:load
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

Un commentaire peut prendre différentes formes, à vous de voir, mais il doit au moins contenir :

- un nom d'auteur (champs de texte libre),
- la date de création du commentaire (générée automatiquement)
- une note de 1 à 5,
- le texte du commentaire.


Créer une classe **persistante** `Commentaire` liée avec une relation un vers plusieurs  à la classe `Musee`.
**Attention** au sens de la relation. Un musée peut avoir plusieurs commentaires et un commentaire ne concerne qu'un seul musée.


![Modèle UML Musée Complet]({{ site.baseurl }}/images/UML-musee-full-infoweb.svg)


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
<div id="map" style="height: 500px;     page-break-inside:avoid;
"></div>
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

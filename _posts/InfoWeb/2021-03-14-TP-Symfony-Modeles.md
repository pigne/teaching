---
layout: post
title: TP Symfony - Gestion des modèles de données
categories:  
- InfoWeb
- lab
published: true
author: Yoann Pigné
published: true
---










Ce TP est une mise en application du [cours](http://pigne.org/teaching/infoweb/lecture/Symfony-Modeles) présenté en classe. Le but principal est la **création de modèles de données persistants** à l'aide de **Doctrine**.



## Groupes et GIT


Ce projet est un TP de groupe. Les groupes peuvent êtres constitués de 2 ou 3 personnes. **Pas plus, pas moins**.  

L'organisation du travail de groupe, la répartition de tâches et l'équilibre des contributions de chacun,  font partit du travail demandé et seront pris en compte dans l'évaluation. 

Les projets GIT sont à créer sur la forge de l'université. Le contenu et la régularité de validations (commits) attestera des contributions de chacun. 

Ne pas oublier de donner les droits *developer* aux enseignants (messieurs Fournier et Pigné). 

Ce projet débute le TP final du cours d'InfoWeb. Ce dépot GIT va être utilisé/amélioré jusqu'au dernier TP. 


Chaque groupe constitué **doit** désigner un **référent** qui se charge de créer le projet GIT et le projet Symfony, puis d'envoyer un courriel aux enseignants avec les noms des membres, le numéro de sujet choisi et l'URL du projet. 

## Échéances 

- Le courriel du référent indiquant la composition du groupe, le n° de sujet et l'URL du projet doit être envoyé **avant le 17 mars**.

- Les commits concernant ce TP doivent être publiés (push) **avant ce dimanche 21 mars à 20h**. 

- Pour information, la seconde partie du TP, qui sera présentée la semaine prochaine sera a rendre pour le dimanche 28 mars. 


## Deux sujets au choix

Vous avez le choix entre deux sujets de TP. N'en choisissez qu'un seul !

Le **sujet n°1** consiste à utiliser à nouveau votre base du Projet BD du premier semestre et de refaire un site *CRUD* entièrement avec Symfony. L'avantage de ce sujet est que vous maîtrisez la structure de la base. L'inconvénient est que c'est une structure parfois complexe avec de nombreuses associations. 

Le **sujet n°2** consiste à utiliser une nouvelle source de données, plus simple, qui ne contient qu'une seule entités principale et une seule association 1-n. L'avantage de ce sujet est la simplicité du modèle de données. L'inconvénient est qu'il y a un travail d'importation et d'adaptation des données à partir d'une source au format texte (CSV, ou JSON). 


Indications valable pour  les **deux sujets** : 

- Reprendre les étapes du [tp précédent](http://pigne.org/teaching/infoweb/lab/TP_Introduction_Symfony) pour créer un nouveau projet spécialement pour cette nouvelle app. **Attention** : seul le **référent** crée le projet Symfony et l'ajoute dans le GIT. Les autres membres n'ont qu'a faire un `git clone ...` et un `composer install`  dans la racine du projet.

- Reprendre les étapes du [cours précédent](http://ppigne.org/teaching/infoweb/lecture/Symfony-Modeles) pour **configurer la base de donnée**  et **créer les entités** rapidement avec les commandes de la console. 
**Attention** : pour éviter les conflits avec le fichier `.env` il est bon que chacun configure sa base de donnée locale (la variable `DATABASE_URL`) dans un fichier `.env.local` non versionné.  

## Sujet n°1

En reprenant les bases de données réalisée en Projet BD, réaliser les entités Symfony et les contrôleurs associées à ces données permettant de lister toutes les données des tables, de visualiser une donnée en fonction de son *id* et d'éditer les données. Par exemple, pour une table `nomRelation`, les routes suivantes sont attendues :

- `/nomRelations`
- `/nomRelation/{id}`
- `/nomRelation/insert/`
- `/nomRelation/update/{id}`
- `/nomRelation/delete/{id}`

Les pages générées doivent répondre à un design *responsive* homogène.

Dans cette étape aucun formulaire n'est demandé, les méthodes d'éditions devront simplement insérer des données aléatoires ou modifier de façon arbitraire les enregistrements ciblés.

Les tables associatives *many-to-many* ne seront pas considérées.
    

Chaque entité sera associé à un contrôleur spécifique afin de favoriser le travail de chacun des membres de l'équipe.


## Sujet n°2

On propose de traiter la liste des établissements d'enseignement de premier et second degré. On souhaite pouvoir **lister** les établissements en fonction de leur localisation (commune, département, académie, région). Outre la visualisation, on souhaitera plus tard pouvoir **créer**, **modifier** et **supprimer** un établissement. Enfin on veut mettre en place un mécanisme de commentaires sur ces établissements.

### une entité

Une entité principale `Établissement` doit être conçue. Le schéma exacte est a déterminer en fonction des informations à votre disposition. 

A minima, un établissement aura les champs suivants : 

- une appellation officielle (son nom)
- une dénomination principale : sa nature (collège, lycée...)
- un secteur (public ou privé)
- des coordonnées GPS numériques longitude et latitude
- une adresse
- un département
- une commune 
- une région 
- une académie
- une date d'ouverture

Le champ "secteur" sera traité comme une énumération. 

### un contrôleur

Un contrôleur principal permettra les actions *CRUD* sur ce model. Pour l'instant il n'est pas demandé de faire des formulaires de création,  de modification ou de suppression. On se concentre sur les routes de visualisation. 

La visualisation par liste doit se faire par catégorie (département, région, commune, académie). On doit donc prévoir les routes adéquates par exemple : 

- `/etablissements/departement/:code_departement`
- `/etablissements/academie/:code_academie`
- etc.


### Import des données 

Des données sources sont disponibles sous Licence Ouverte pour alimenter ce model. Les noms des champs du model ne doivent pas forcément correspondre à ceux du fichier. 

- site de la ressource : <https://www.data.gouv.fr/fr/datasets/adresse-et-geolocalisation-des-etablissements-denseignement-du-premier-et-second-degres-1/#_>
- notice expliquant  les différents champs <https://www.data.gouv.fr/fr/datasets/r/eb9a4edc-e2f3-4ff4-bb03-a41cf7acf692> 
- lien de téléchargement direct au format CSV : <https://data.education.gouv.fr/explore/dataset/fr-en-adresse-et-geolocalisation-etablissements-premier-et-second-degre/download?format=csv&amp;timezone=Europe/Berlin&amp;use_labels_for_header=false>


On peut bien sur ouvrir ce fichier CSV avec Excel, récupérer les colonnes intéressantes et se débattre un peu avec DataGrip ou PhpMyAdmin pour importer les données directement dans la base... Mais ce n'est pas la bonne méthode. 

La  bonne méthode c'est d'écrire un script d'import en PHP qui va télécharger les données et créer des instance de l'entité `Établissement` et les faire persister en base. Pour ça on utilise DoctrineFixturesBundle: <https://symfony.com/doc/current/bundles/DoctrineFixturesBundle/index.html>

On aura besoin d'interpréter les données en fonction du format utilisé : 

- CSV : [`fgetcsv()`](https://www.php.net/manual/fr/function.fgetcsv.php)
- JSON : [`json_decode()`](https://www.php.net/manual/fr/function.json-decode.php)



### Des commentaires 


On souhaite que le site affiche une liste de commentaires associés à chaque établissement. 

Un commentaire peut être constitué de : 
- un nom d'auteur (champs de texte libre),
- la date de création du commentaire (générée automatiquement)
- une note de 1 à 5,
- le texte du commentaire.

Prévoir cette nouvelle entité en relation 1-n avec l'entité établissement. **Attention** au sens de la relation. Un établissement peut avoir plusieurs commentaires et un commentaire ne concerne qu'un seul établissement.

Se concentrer sur l'affichage  plutôt que sur la création des commentaire. Les formulaires seront abordés plus tard. 




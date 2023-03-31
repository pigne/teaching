---
layout: post
title: TP Symfony - Gestion des formulaires
categories:  
- InfoWeb
- lab
published: true
author: Yoann Pigné
update: 2023-03-30
---

On  continue le travail commencé lors du [TP Précédent](http://pigne.org/teaching/infoweb/lab/TP-Symfony-Modeles).


Suivre les instructions en fonction du n° de sujet choisi la semaine précédente. 

## Échéance (pour tous les sujets)

Cette dernière partie doit toujours être versionnée et faire l'objet de validations régulières de la part de tous les membres du groupe. Les dépôts GIT seront relevés le **dimanche 27 mars 2022 à 20h (CEST)**, pour évaluation.

## Sujet 1

Il y a **une seule** tâches à réaliser pour les groupes ayant choisi le sujet n°1 : les **formulaires**.


### Gestion des formulaires

En suivant le [cours](https://pigne.org/teaching/infoweb/lecture/Symfony-Forms) créer  des classes de *type* (e.g. `MonEntitéType`) dans le dossier `src/Form/` qui permettent la création de formulaires pour créer/modifier les entités (e.g. `MonEntité`) de votre base. 

Chaque entité du modèle doit posséder son propre *type*.

On peut s'aider de la commande : `php bin/console make:form`



Adapter les **contrôleurs**, les **entités**, et les **vues** pour utiliser correctement les formulaires Symfony et pour que tous les champs soient correctement **validés** lors de la saisie des formulaires.

La **validation** doit être gérée au niveau des entités, comme cela a été présenté en cours. 



## Sujet 2

Il y a **deux** tâches à réaliser pour les groupes ayant choisi le sujet n°2 : 

  - Les formulaires
  - une vues cartographique 

### Gestion des formulaires

En suivant le [cours](https://pigne.org/teaching/infoweb/lecture/Symfony-Forms) créer une classe `EtablissementType` dans le dossier `src/Form/` qui permet la création de formulaires pour créer/modifier les entités `Etablissement`.

Faire de même pour l'entité `Commentaire`.

On peut s'aider de la commande : `php bin/console make:form`

Adapter les **contrôleurs**, les **entités**, et les **vues** pour utiliser correctement les formulaires Symfony et pour que tous les champs soient correctement **validés** lors de la saisie des formulaires d'`Etablissement` et de `Commentaire`.

La **validation** doit être gérée au niveau des entités, comme cela a été présenté en cours. 

### Vue Cartographique

Ajouter une vue/route vers une visualisation des établissements sous forme d'un ensemble de marqueurs sur une carte. 

Pour des raisons de performance, on se restreindra au filtre par commune avec, par exemple, une route du type :
```php
etablissement/cartographieCommune/:idCommune
```

PHP et surtout TWIG sont capable de générer bien plus que des pages web. Il est tout a fait possible de générer du javascript.

Prenons l'exemple de la bibliothèque [Leaflet](http://leafletjs.com). Le code suivant permet de créer une carte et d'y ajouter des _markers_.

En utilisant par exemple les blocs `stylesheets` et `javascript` par défaut du fichier  TWIG  `base.twig.html` il suffit d'ajouter la bibliothèque en référence dans la page web :

```html
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.7.1/dist/leaflet.css"
  integrity="sha512-xodZBNTC5n17Xt2atTPuE1HxjVMSvLVW9ocqUKLsCC5CXdbqCmblAshOMAS6/keqq/sMZMZ19scR4PsZChSR7A=="
  crossorigin=""/>
<script src="https://unpkg.com/leaflet@1.7.1/dist/leaflet.js"
  integrity="sha512-XQoYMqMTK8LvdxXYG3nZ448hOEQiglfqkJs1NOQV44cWnUrBc8PkAOcXy20w0vlaXaVUearIOBhiXZ5V3ynxwA=="
  crossorigin=""></script>
```

puis de définir un élément du *markup* dans le TWIG qui recevra la carte :

```html
<div id="map" style="height: 500px;"></div>
```

enfin le script suivant (qu'il faut aussi placer dans le template `TWIG`) fera le reste. On Note  qu'il suffit de modifier le tableau `data` (avec `TWIG`) pour afficher d'avantage de  points.

```javascript
<script>
const draw_map = (data) => {  
  const map = L.map('map')
    .fitBounds(data.map((d) => [d.lat,d.lon]))
    .addLayer(L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
    }));
  data.forEach((etablissement) => {
    L.marker([etablissement.lat, etablissement.lon]).addTo(map).bindPopup("<b>"+etablissement.nom+"</b>");
  });
};

const data = [
  {
    nom:'Ecole élémentaire Dauphine',
    lat:'49.48957543806298',
    lon:'0.11556983645413253'
  },
  {
    nom:'Collège Descartes',
    lat:'49.51941345248356',
    lon:'0.10888053221663069'
  },
  {
    nom:'Lycée général François Ier',
    lat:'49.49610537217045',
    lon:'0.1137094907033469'
  },
  /*
    TODO: à vous d'ajouter la suite dynamiquement avec TWIG...
  */
];

draw_map(data);
</script>
```

Résultat :


<link rel="stylesheet" href="https://unpkg.com/leaflet@1.7.1/dist/leaflet.css"
  integrity="sha512-xodZBNTC5n17Xt2atTPuE1HxjVMSvLVW9ocqUKLsCC5CXdbqCmblAshOMAS6/keqq/sMZMZ19scR4PsZChSR7A=="
  crossorigin=""/>
<script src="https://unpkg.com/leaflet@1.7.1/dist/leaflet.js"
  integrity="sha512-XQoYMqMTK8LvdxXYG3nZ448hOEQiglfqkJs1NOQV44cWnUrBc8PkAOcXy20w0vlaXaVUearIOBhiXZ5V3ynxwA=="
  crossorigin=""></script>

<div id="map" style="height: 500px;     page-break-inside:avoid;
"></div>
<script>
const draw_map = (data) => {  
  const map = L.map('map')
    .fitBounds(data.map((d) => [d.lat,d.lon]))
    .addLayer(L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
    }));
  
  let marker;
  data.forEach((etablissement) => {
    marker = L.marker([etablissement.lat, etablissement.lon]).addTo(map).bindPopup("<b>"+etablissement.nom+"</b>");
  });

  marker.openPopup();
};


const data = [
  {
    nom:'Ecole élémentaire Dauphine',
    lat:'49.48957543806298',
    lon:'0.11556983645413253'
  },
  {
    nom:'Collège Descartes',
    lat:'49.51941345248356',
    lon:'0.10888053221663069'
  },
  {
    nom:'Lycée général François Ier',
    lat:'49.49610537217045',
    lon:'0.1137094907033469'
  },
];


draw_map(data);

</script>


<!-- 
## Gestion d'authentification et de droits d'utilisateurs

Pour notre application  on souhaite les droits suivants :

- les administrateurs :
  - peuvent créer/modifier/supprimer des musées
- les utilisateurs non-administrateurs:
  - peuvent commenter sur les musées
- les visiteurs non-connectés:
  - peuvent consulter les musées et et les commentaires

Les administrateurs peuvent faire ce que les utilisateurs veuvent faire.

Les utilisateurs connectés peuvent faire ce que les visiteurs peuvent faire.


Symfony possède un mécanisme de base pour la gestion d'utilisateurs. A l'aire un _bundle_ supplémentaire, il est très facile de mettre en place des mécanismes classiques de connexion, déconnexion, et enregistrement de nouveaux utilisateurs.

### Le bundle FOSUserBundle

Suivre les 7 étapes de configuration du bundle ici :
[https://symfony.com/doc/master/bundles/FOSUserBundle/index.html](https://symfony.com/doc/master/bundles/FOSUserBundle)

### _Fixtures_ pour les utilisateurs

Pour simplifier la manipulation des utilisateurs on crée une nouvelle classe de _fixture_ qui permet de générer des utilisateurs avec leur mot de passe. Dans l'exemple suivant la classe  `LoadUserData`  dans le fichier `src/AppBundle/DataFixtures/ORM/LoadUserData.php` permet de créer 2 utilisateurs :

| login | mdp | role |
|-------|-----|------|
| admin | admin | ROLE_ADMIN |
| user  | user | ROLE_USER |


```php
<?php
// src/AppBundle/DataFixtures/ORM/LoadUserData.php

namespace AppBundle\DataFixtures\ORM;

use Doctrine\Common\DataFixtures\FixtureInterface;
use Doctrine\Common\Persistence\ObjectManager;
use Symfony\Component\DependencyInjection\ContainerAwareInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;

class LoadUserData implements FixtureInterface, ContainerAwareInterface
{
    private $container = 'Private';


    public function load(ObjectManager $manager)
    {

        // Get our userManager, you must implement `ContainerAwareInterface`
        $userManager = $this->container->get('fos_user.user_manager');

        // Create our user and set details
        $admin = $userManager->createUser();
        $admin->setUsername('admin');
        $admin->setEmail('admin@domain.com');
        $admin->setPlainPassword('admin');
        //$user->setPassword('3NCRYPT3D-V3R51ON');
        $admin->setEnabled(true);
        $admin->setRoles(array('ROLE_ADMIN'));

        // Update the user
        $userManager->updateUser($admin, true);


        // Create our user and set details
        $user = $userManager->createUser();
        $user->setUsername('user');
        $user->setEmail('user@domain.com');
        $user->setPlainPassword('user');
        //$user->setPassword('3NCRYPT3D-V3R51ON');
        $user->setEnabled(true);
        $user->setRoles(array('ROLE_USER'));

        // Update the user
        $userManager->updateUser($user, true);




        $manager->persist($admin);
        $manager->persist($user);
        $manager->flush();

    }

    public function setContainer(ContainerInterface $container = null)
    {
        $this->container = $container;
    }
    /**
     * @return ContainerInterface
     */
    public function getContainer()
    {
        return $this->container;
    }

}
```

On génère les données avec la commande :

```bash
php bin/console doctrine:fixtures:load
```

### Gestion des droits

Les droits se gèrent ensuite en associant des _roles_ à des _routes_  grâce à des expressions rationnelles dans le fichier `security.yml`

```yml
# ...
access_control:
        - { path: ^/login$, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/register, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/resetting, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/musee/[0-9]+/edit$, role: ROLE_ADMIN }
        - { path: ^/musee/[0-9]+/comment$, role: ROLE_USER }
        - ...
```

### Sécurisation des contrôleurs

On utilise la fonction `denyAccessUnlessGranted` dans les actions de contrôleurs pour s'assurer qu'un utilisateur est autorisé a faire cette action

```php
<?php
class SomeController {

  public function someAction($name)
  {
    // Exception levée si l'utilisateur n'est pas administrateur
    $this->denyAccessUnlessGranted('ROLE_ADMIN', null, 'Unable to access this page!');
// ...

```

### Sécurisation des vues

Dans les vues on va choisir ce que l'on veut montrer en fonctions des droits de l'utilisateur :

{% raw %}
```liquid
{% if is_granted('ROLE_ADMIN') %}
    <a href="...">Delete</a>
{% endif %}

{% if is_granted('ROLE_USER') %}
    <h2>Donnez votre avis</h2>
    {{ form_start(comment_form) }}
    {{ form_widget(comment_form) }}
    {{ form_end(comment_form) }}
{% endif %}
```
{% endraw %}
### Surcharger les templates de FOSUserBundle

[Overriding Default FOSUserBundle Templates](http://symfony.com/doc/current/bundles/FOSUserBundle/overriding_templates.html) -->

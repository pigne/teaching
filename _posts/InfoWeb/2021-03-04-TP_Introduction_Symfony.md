---
layout: post
title: TP d'introduction à Symfony
categories:
- InfoWeb
- lab
author: Yoann Pigné
published: true
update: 2025-02-25
---



Symfony est entre autres un framework Web. Il contient un ensemble de ressources qui facilitent la création d'applications Web utilisant le _design pattern_ MVC (_Model View Controller_).

## TP + Questions

Il y a 2 choses à faire dans ce TP :

1. Suivre les étapes afin de réaliser le TP.
2. Répondre en parallèle aux questions qui sont posées tout au long de l'énoncé. Les réponses sont à écrire dans le [questionnaire Eureka](https://eureka.univ-lehavre.fr/mod/quiz/view.php?id=5323se trouvant sur la page du cours.

Il est possible de travailler en binôme pour ce TP. En revanche, les réponses aux questions dans Eureka doivent être répondues par tout le monde.

Ce TP doit être versionné avec GIT et être partagé en utilisant la [forge de l'université](https://www-apps.univ-lehavre.fr/forge). Ne pas oublier d'ajouter les enseignants (`pigne` et `fournied`) au projet avec le statut *developer*.

Une fois le projet créé sur la forge, envoyer un mail à Dominique Fournier <dominique.fournier@univ-lehavre.fr> et Yoann Pigné <yoann.pigne@univ-lehavre.fr> avec le titre "`[L3 InfoWeb] TP intro Symfony`". Ce mail indiquera les **nom** et **prénom** de chaque membre du groupe ainsi que l'URL du projet.

## Installations préalables

Pour commencer à utiliser Symfony, il faut installer `composer` ainsi que le client `symfony`.

### Composer

Pour installer Composer, on peut suivre les instructions en fonction de l'OS utilisé : <https://getcomposer.org>

#### Composer sur les machines de TP de l'UFR

1. On télécharge Composer avec la commande :
   ```bash
   mkdir -p ${HOME}/.composer/bin
   wget https://raw.githubusercontent.com/composer/getcomposer.org/76a7060ccb93902cd7576b67264ad91c8a2700e2/web/installer -O - -q | php -- --install-dir=${HOME}/.composer/bin --filename=composer
   ```
2. On ajoute la ligne suivante au fichier `${HOME}/.profile`  (il faut ouvrir ce fichier avec un éditeur de texte) :
   ```bash
   export PATH="$HOME/.composer/bin:$PATH"
   ```
3. Enfin, on recharge l'environnement :
   ```sh
   source ${HOME}/.profile
   ```

**Note :** En ouvrant un autre terminal, on risque de ne plus avoir accès à `composer`. Il suffit de refaire la dernière commande : `source ${HOME}/.profile` ou bien de redémarrer sa session.

### Symfony

Pour installer Symfony, on suit les instructions en fonction de l'OS utilisé : <https://symfony.com/download>

#### Symfony sur les machines de TP de l'UFR

1. On télécharge la version binaire de Symfony avec la commande :
   ```sh
   wget https://get.symfony.com/cli/installer -O - | bash
   ```
2. On ajoute la ligne suivante au fichier `${HOME}/.profile` (il faut ouvrir ce fichier avec un éditeur de texte):
   ```sh
   export PATH="$HOME/.symfony5/bin:$PATH"
   ```
3. Enfin, on recharge l'environnement :
   ```sh
   source ${HOME}/.profile
   ```

**Note :** En ouvrant un autre terminal, on risque de ne plus avoir accès à `symfony`. Il suffit de refaire la dernière commande : `source ${HOME}/.profile` ou bien de redémarrer sa session.

## Création d'un premier projet

On crée de nouveaux projets Symfony avec la commande `symfony`.

```bash
symfony new projet_hello --version=lts --webapp
cd projet_hello
```

### GIT

Un `git init` ainsi que quelques commits ont été faits pour vous dans le projet. À vous d'ajouter ce projet sur la forge en ajoutant un `remote` :  

1. **Ajouter un `remote`** pour connecter votre projet local à un projet sur la forge. Ce projet n'existe pas encore sur la forge, mais il sera créé lors de l'étape suivante. Pour ajouter le `remote` :  
   ```bash
   git remote add origin https://www-apps.univ-lehavre.fr/forge/UTILISATEUR/PROJET.git
   ```
   où `UTILISATEUR` est votre nom d'utilisateur sur la forge et `PROJET` est le nom de votre projet (vous devez choisir ce nom).  

2. **Pousser le projet sur la forge** (ce qui va le créer) avec la commande :  
   ```bash
   git push -u origin master
   ```

Pour le binome qui n'a pas créé le projet, il faut cloner le projet avec la commande :
```bash
git clone https://www-apps.univ-lehavre.fr/forge/UTILISATEUR/PROJET.git
```

puis installer les dépendances avec la commande : 

```bash
composer install
```



## Analyse du projet de base

Examiner attentivement le contenu du projet (dossier `projet_hello`). Les dossiers et sous-dossiers sont organisés de sorte à ne pas mélanger les choses qui n'ont rien à voir ensemble (séparation des responsabilités).

```bash
./
├── .env
├── .env.dev
├── .env.test
├── .git/
├── .gitignore
├── assets/
├── bin/
├── compose.override.yaml
├── compose.yaml
├── composer.json
├── composer.lock
├── config/
├── importmap.php
├── migrations/
├── phpunit.xml.dist
├── public/
├── src/
├── symfony.lock
├── templates/
├── tests/
├── translations/
├── var/
└── vendor/
```

Le dossier `config/` contient les paramètres de l'application. On modifiera ce dossier pour configurer l'application ou changer son comportement par défaut (e.g. : chargement de modules complémentaires).

Le dossier `src/` contient les sources de notre application. C'est le dossier qui nous intéresse le plus ici.

Le dossier `templates/` contient les fichiers *templates* (les patrons) TWIG pour faciliter la génération de pages Web.

Le dossier `tests/` contient comme on s'en doute, les tests.


<div class="question">

### Questions 1, 2, 3 et 4

Répondre aux questions suivantes sur le [questionnaire se trouvant sur Eureka](https://eureka.univ-lehavre.fr/mod/quiz/view.php?id=5323) :

1. En examinant le contenu des fichiers et en cherchant sur le site de Symfony, expliquer l'utilité du  dossier `vendor/`.
2. En examinant le contenu des fichiers et en cherchant sur le site de Symfony, expliquer l'utilité du  dossier  `public/`.
3. En examinant le contenu des fichiers et en cherchant sur le site de Symfony, expliquer l'utilité du  dossier  `assets/`.
4. Quel fichier dois-je modifier pour configurer les identifiants de bases de données afin que Symfony puisse accéder à la base de données ?

</div>

## Démarrer l'application

L'étape suivante est de démarrer l'application. On utilise le serveur web interne de PHP plutôt qu'Apache pour le développement.

```bash
symfony server:start
```

On peut voir le site dans un navigateur, à l'adresse [http://localhost:8000](http://localhost:8000).



## Contrôleur, Action et Route

La page visible à l'adresse `http://localhost:8000/` est une page par défaut, elle correspond à une **route** et à une **action** dans le **contrôleur** par défaut.

Ces trois termes sont importants :

- Un **contrôleur** est la classe principale qui gère un ensemble d'**actions**.
- Une **action** est une méthode de classe du contrôleur, c'est un peu comme un des services possibles proposés par le contrôleur.
- Une **route** est la partie qui vient après le nom de domaine dans une URL au sens de HTTP. Par exemple, dans l'URL `https://www.example.com/truc/machin`, la route est `/truc/machin`.

Il faut configurer le contrôleur pour que ses actions soient liées à des routes.

## Création d'un contrôleur

Nous allons créer un contrôleur, des actions et des routes associées.

**Créer** un nouveau fichier `"src/Controller/HelloController.php"` avec le contenu suivant :

```php
<?php

namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\Routing\Annotation\Route;
use Symfony\Component\HttpFoundation\Response;

class HelloController extends AbstractController
{
  #[Route("/helloRandom")]
  public function randomNameAction(): Response
  {
    return new Response(
      "<html><body><h1>Hello " .
        self::generateRandomName() .
        "</h1></body></html>"
    );
  }

  static function generateRandomName(): string
  {
    $nouns = [
      "Circle", "Cone", "Cylinder", "Ellipse", "Hexagon",
      "Irregular Shape", "Octagon", "Oval", "Parallelogram",
      "Pentagon", "Pyramid", "Rectangle", "Semicircle", "Sphere",
      "Square", "Star", "Trapezoid", "Triangle", "Wedge", "Whorl",
    ];
    $adjectives = [
      "Amusing", "Athletic", "Beautiful", "Brave", "Careless",
      "Clever", "Crafty", "Creative", "Cute", "Dependable",
      "Energetic", "Famous", "Friendly", "Graceful", "Helpful",
      "Humble", "Inconsiderate", "Likable", "Middle-Class", "Outgoing",
      "Poor", "Practical", "Rich", "Sad", "Skinny", "Successful", "Thin",
      "Ugly", "Wealthy",
    ];
    return $adjectives[array_rand($adjectives)] .
      " " .
      $nouns[array_rand($nouns)];
  }
}
```

On peut voir fonctionner ce nouveau contrôleur avec l'URL : [http://localhost:8000/helloRandom](http://localhost:8000/helloRandom) et recharger la page plusieurs fois...

<div class="question">

### Question 5

Comment fait-on le lien entre une route (URL) de l'application et une action d'un contrôleur ?

</div>

## Lancer les tests

Il faut maintenant tester ce contrôleur. **Écrire** le fichier de test `tests/Controller/HelloControllerTest.php` (il faut créer le dossier `Controller/` dans `tests/` s'il n'existe pas) :

```php
<?php
namespace App\Tests\Controller;

use App\Controller\HelloController;
use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

class HelloControllerTest extends WebTestCase
{
  public function testHelloRandomRoute()
  {
    $client = static::createClient();

    $crawler = $client->request("GET", "/helloRandom");

    $this->assertEquals(200, $client->getResponse()->getStatusCode());
    $this->assertStringContainsString("Hello ", $crawler->filter("h1")->text());
  }

  function testRandomNameGenerator()
  {
    $rdName = HelloController::generateRandomName();
    $this->assertGreaterThan(1, strlen($rdName));
  }
}
```

On exécute les tests avec `PHPUnit` (l'équivalent de `JUnit` en Java pour `PHP`) :

```sh
php bin/phpunit
```

On devrait obtenir :

```
OK (2 tests, 3 assertions)
```



<div class="question">

### Questions 6

Dans la classe de test `HelloControllerTest`, expliquer ce que fait la méthode `testHelloRandomRoute`.

</div>

<div class="question">

### Question 7

Dans la classe de test `HelloControllerTest`, expliquer ce que fait la méthode `testRandomNameGenerator`.
</div>
## Route et action paramétriques

On souhaite créer une action qui dépende des paramètres de la route (une route paramétrique).

**Ajouter** la méthode suivante à la classe `HelloController` :

```php
  #[Route("/hello/{name}", defaults: ["name" => ""])]  
  public function nameAction($name): Response
  {
    if ($name == "") {
      $name = self::generateRandomName();
    }

    return new Response("<html><body><h1>Hello $name</h1></body></html>");
  }
```

**Exécuter** cette méthode avec la route :  [http://localhost:8000/hello/Albus%20Perceval%20Wulfric%20Brian%20Dumbledore](http://localhost:8000/hello/Albus%20Perceval%20Wulfric%20Brian%20Dumbledore)

### Tester la route

**Écrire** un test dans la classe `HelloControllerTest` qui vérifie que la méthode fonctionne comme prévu. Vérifier que la page web générée salue bien le Professeur Dumbledore quand son nom est passé dans l'URL. Vérifier aussi que le nom est aléatoire quand aucun paramètre n'est donné ([http://localhost:8000/hello](http://localhost:8000/hello)).

<div class="question">

### Questions 8

Coller votre classe de test `HelloControllerTest` dans le questionnaire Eureka (Question 8).

</div>

## Création d'une vue

On souhaite maintenant générer des pages Web complètes et pas seulement une chaîne de caractères passée dans l'objet `Response`.

Symfony utilise le langage de _template_ `twig`.

La méthode `render()` du contrôleur permet d'utiliser un template `twig` pour fabriquer une réponse et retourner un objet `Response`.

**Ouvrir** et **lire** le fichier `templates/base.html.twig`.

**Créer** un template pour le contrôleur Hello dans le fichier `templates/hello.html.twig` :

{% raw %}

```html
{% extends 'base.html.twig' %}

{% block title %}Hello!{% endblock %}

{% block body %}
    <div id="container">
        <div id="hello">
            <h1>Hello <i>{{ name }}</i>!</h1>
        </div>
    </div>
{% endblock %}

{% block stylesheets %}
<style>
    body { background: #F5F5F5; font: 18px/1.5 sans-serif; }
    h1, h2 { line-height: 1.2; margin: 0 0 .5em; color: #4343AA;}
    h1 { font-size: 36px; }
    #container { background: #FFF; margin: 1em auto; max-width: 800px; width: 98%; padding: 2em; box-sizing: border-box;}

    @media (min-width: 768px) {
        #container {  margin: 2em auto; }
    }
</style>
{% endblock %}
```
{% endraw %}


**Modifier** l'action `nameAction` du contrôleur `HelloController` afin d'utiliser le _template_ qui vient d'être créé. On s'inspirera de la [documentation en ligne](https://symfony.com/doc/current/controller.html#rendering-templates).

<div class="question">

### Questions  9

Copier le code de l'action `nameAction` dans la question 9 du questionnaire Eureka.

</div>

## Sessions

On souhaite que l'application se souvienne de nous. À chaque fois que l'action `nameAction` est appelée avec un paramètre (avec un nom), on veut qu'il soit **sauvegardé** dans une **session** (en remplaçant éventuellement celui qui existait déjà). À chaque fois que l'action `nameAction` est appelée **sans** paramètre, alors on veut retrouver le paramètre précédemment **enregistré dans la session**. Finalement, si `nameAction` est appelé sans paramètre pour la première fois (rien n'existe dans la session), alors on génère un nom aléatoirement, que l'on sauvegarde dans la session.

Par exemple, si j'appelle une fois ([http://localhost:8000/hello/You](http://localhost:8000/hello/You)), cela va afficher `"Hello You!"`. Si ensuite j'appelle (dans le même navigateur) ([http://localhost:8000/hello](http://localhost:8000/hello)), cela doit également afficher `"Hello You!"`. En revanche, si j'appelle pour la première fois l'action sans paramètres ([http://localhost:8000/hello](http://localhost:8000/hello)), alors on obtient un nom aléatoire qui va être stocké pour les appels futurs.

**Modifier** l'action `nameAction` pour qu'elle se comporte comme décrit ci-dessus, en utilisant la [documentation Symfony sur les sessions](https://symfony.com/doc/current/session.html).

<div class="question">

### Question 10

À quoi sert le fichier `composer.json` qui se trouve à la racine du projet ?
</div>

<div class="question">

### Question 11

Copier le code de `nameAction` dans Eureka (Question 11).  

</div>

**Écrire** de nouveaux tests (en ajoutant des méthodes dans la classe `tests/Controller/HelloControllerTest.php`) pour vérifier le nouveau fonctionnement.

<div class="question">

### Question 12

Copier le code de ces tests dans Eureka (Question 12).  
  
</div>


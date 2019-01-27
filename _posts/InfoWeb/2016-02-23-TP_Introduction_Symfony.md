---
layout: post
title: TP d'introduction à Symfony
categories:
- InfoWeb
- lab
author: Yoann Pigné
published: false
---



Symfony est entre autres un framework Web. Il contient un ensemble de ressources qui facilitent la création d'applications Web utilisant le _design pattern_ MVC (_Model View Controller_).



## TP + Questions

Il y a 2 choses à faire dans ce TP :

1. Suivre les étapes afin de réaliser le TP.
2. Répondre aux questions qui sont posées tout au long de l'énoncé. Les réponses sont à écrire dans le [questionnaire Eureka](https://eureka.univ-lehavre.fr/mod/quiz/view.php?id=45530) se trouvant sur la page du cours.

Il est possible de travailler en binôme pour ce TP. En revanche, les réponses aux questions dans Eureka doivent être répondues par tout le monde. 

 Même si cela n'est pas explicitement demandé par la suite, le projet suivant devra être géré avec GIT en utilisatn la [forge de l'université](https://www-apps.univ-lehavre.fr/forge). Ne pas oublier de m'ajouter (`pigne`) dans le projet. 



## Installation de Symfony

Pour commencer à utiliser Symfony il faut télécharger et installer le script d'installation Symfony. Plusieurs options s'offrent à nous.

### Installation classique

La méthode la plus simple,  quel que soit votre OS, est celle décrite dans la documentation officielle  : [https://symfony.com/doc/current/book/installation.html](https://symfony.com/doc/current/book/installation.html)

- [Mac et Linux](https://symfony.com/doc/current/book/installation.html#linux-and-mac-os-x-systems)
- [Windows](https://symfony.com/doc/current/book/installation.html#windows-systems)


### Pour les machines de TP de l'université

Pour les **machines de TP de l'université**, on va installer Symfony dans le dossier utilisateur car nous n'avons pas les droits d'écriture dans `/usr/local/...`

```bash
mkdir ${HOME}/bin
curl -LsS https://symfony.com/installer -o ${HOME}/bin/symfony
chmod a+x ${HOME}/bin/symfony
```


On modifie la variable d'environnement `$PATH` pour que l'exécutable `symfony` soit disponible partout. Attention il ne faut faire cette manip qu'une seule fois :

Attention : la commande suivante ne doit être exécuté qu'une seule fois !

```bash
echo "export PATH=$PATH:${HOME}/bin" >> ${HOME}/.bash_profile
source ${HOME}/.bash_profile
```


### Installation dans PHPStorm (facultatif)

Il est aussi très facile d'utiliser Symfony avec un IDE. Le plus populaire et plus facile à utiliser à l'heure actuelle (2016) est probablement PHPStorm. celui-ci n'est ni libre ni gratuit, mais une licence étudiant gratuite est accessible. 

Il est normalement possible d'installer PHPStorm sur les machines de TP de l'université. mais ce n'est pas obligatoire. 

Pour installer Symfony dans PHPStorm, aller dans les _préférences_, puis _plugins_, puis cliquer sur "_Browse repository_", puis rechercher le plugin "Symfony Plugin". Installer et redémarrer.

## Création d'un premier projet

On a installé l'_installer_ qui nous permet de créer de nouveaux projets. On commence donc un projet Symfony avec l'_installer_.


### Mac & Linux

```bash
symfony new projet_hello
cd projet_hello
```

### Windows

Dans un dossier de travail, on copie l'_installer_ `symfony` avant de l'exécuter :

```bash
move symfony c:\dossier_travail
cd c:
cd dossier_travail
php symfony new projet_hello
cd projet_hello
```


### PHPStorm ou autre IDE

En fonction de l'IDE les étapes diffèrent mais en gros : "fichier" /  "nouveaux" / "nouveaux projet à partir du template symfony" / ...



### GIT 

C'est probablementn le bon moment pour faire un `git init` dans le projet et pour le connecter à un projet sur la forge. 

## Analyse du projet de base

Examiner attentivement le contenu du projet (dossier `projet_hello`). les dossiers et sous-dossiers sont organisés de sorte à ne pas mélanger les choses qui n'ont rien à voir ensemble.

```bash
.
├── README.md
├── app/
├── bin/
├── composer.json
├── composer.lock
├── phpunit.xml.dist
├── src/
├── tests/
├── var/
├── vendor/
└── web/
```

Le dossier `app/` est le point de départ de l'application. Il contient les classes de base,  les paramètres et les ressources par défaut de l'application. On modifiera ce dossier pour configurer l'application, ajouter des ressources ou changer son comportement par défaut (e.g.: chargement de modules complémentaires).

Le dossier `src/` contient les sources de notre application. C'est le dossier qui nous intéresse le plus ici.

Le dossier `tests/` contient comme on s'en doute les tests unitaires.


<div class="question">

### Questions 1, 2 et 3

Répondre aux questions suivantes sur le [questionnaire se trouvant sur Eureka](https://eureka.univ-lehavre.fr/mod/quiz/view.php?id=45530) :

- En examinant le contenu des fichiers et en cherchant sur le site de Symfony, expliquer l'utilité des dossiers `vendor/` et `web/`.
- Quel fichier dois-je modifier pour configurer les identifiants de bases de données afin que Symfony puisse accéder à la Base de données ?

</div>


## Lancer les Tests Unitaires

La première chose à faire est de lancer les tests unitaires pour s'assurer que tout fonctionne. On exécute les testes avec PHPUnit (l'équivalent du `JUnit` de `Java` pour `PHP`). **Problème** : notre projet Symfony s'attend à ce que `PHPUnit` soit déjà installé dans le système, hors ce n'est pas le cas. Pour installer `PHPUnit` il faut :

- Installer `composer` qui permet de gérer les dépendances du projet.
- Installer `PHPUnit` avec `composer`.

Suivre dans l'ordre les étapes suivantes :

 1) **Constater** qu'il n'y a aucune mention à `PHPUnit` dans le fichier `composer.json` qui se trouve à la racine du projet.

 2) **installer** `composer` :

```bash
php -r "readfile('https://getcomposer.org/installer');" > composer-setup.php
php composer-setup.php
php -r "unlink('composer-setup.php');"
```
Note : ici la [procédure d'installation pour Windows](https://getcomposer.org/doc/00-intro.md#installation-windows).

 3) **installer** `PHPUnit`:

```bash
php composer.phar require phpunit/phpunit
```

4) **Constater** que `phpunit` apparait dans le fichier `composer.json` et qu'un dossier `phpunit` existe dans le dossier `vendor/`

5) **Lancer les tests**:

```bash
php vendor/bin/phpunit tests
```

il devrait y en avoir un seul pour l'instant le résultat devrait être :

```
OK (1 test, 2 assertions)
```

<div class="question">
### Question 4

A quoi sert le fichier `composer.json` qui se trouve à la racine du projet ?
</div>

## Démarrer l'application

L'étape suivante est de démarrer l'application. On utilise le serveur web interne de PHP plutôt qu'Apache pour le développement.

```bash
php bin/console server:run
```

On peut voir le site dans un navigateur, à l'adresse [http://localhost:8000](http://localhost:8000).



## Contrôleur Action et Route

La page visible a l'adresse `http://localhost:8000/` est une page par défaut, elle correspond à une **route** et à une **action** dans le **contrôleur** par défaut.

Ces 3 termes sont importants :

- Un **contrôleur** est la classe principale qui gère un ensemble d'**actions**.
- Une **action** est une méthode de classe du contrôleur, c'est un peu comme un des services  possibles proposés par le contrôleur.
- Une **route** est la partie qui vient après le nom de domaine dans une URL au sens de HTTP. Par exemple dans l'URL `http://www.example.com/truc/machin`, la route est `/truc/machin`.

Il faut configurer le contrôleur pour que ses actions soient liées à des routes.


**Ouvrir** le fichier `src/AppBundle/Controller/DefaultController.php` et **identifier** comment le lien entre une **action** du **contrôleur** et une **route** est réalisé.

<div class="question">

### Question 5

Dans l'action d'un contrôleur, comment fait-on le lien avec une route (URL) de l'application ?
</div>

## Création d'un contrôleur

Nous allons créer un contrôleur des actions et des routes associées.

**Créer** un nouveaux fichier `"src/AppBundle/Controller/HelloController.php"` avec le contenu suivant :

```php
<?php
namespace AppBundle\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\Controller;
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
use Symfony\Component\HttpFoundation\Response;

class HelloController extends Controller
{

    /**
     * @Route("/helloRandom")
     */
    public function randomNameAction(){
        return new Response("<html><body><h1>Hello "
            .self::generateRandomName()
            ."</h1></body></html>");
    }


    static function generateRandomName()
    {
        $nouns = ['Circle', 'Cone', 'Cylinder', 'Ellipse', 'Hexagon',
          'Irregular Shape', 'Octagon', 'Oval', 'Parallelogram', 'Pentagon',
          'Pyramid', 'Rectangle', 'Semicircle', 'Sphere', 'Square', 'Star',
          'Trapezoid', 'Triangle', 'Wedge', 'Whorl'];
        $adjectives = ['Amusing', 'Athletic', 'Beautiful', 'Brave', 'Careless',
          'Clever', 'Crafty', 'Creative', 'Cute', 'Dependable', 'Energetic',
          'Famous', 'Friendly', 'Graceful', 'Helpful', 'Humble', 'Inconsiderate',
          'Likable', 'Middle Class', 'Outgoing', 'Poor', 'Practical', 'Rich',
          'Sad', 'Skinny', 'Successful', 'Thin', 'Ugly', 'Wealth'];
        return $adjectives[array_rand($adjectives)].' '.$nouns[array_rand($nouns)];
    }
}
```


Il faut maintenant tester ce contrôleur. En s'inspirant du test déjà présent écrire le fichier de test `tests/AppBundle/Controller/HelloControllerTest.php` :

```php
<?php
namespace Tests\AppBundle\Controller;

use AppBundle\Controller\HelloController;
use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

class HelloControllerTest extends WebTestCase
{
    public function testHelloRandomRoute()
    {
        $client = static::createClient();

        $crawler = $client->request('GET', '/helloRandom');

        $this->assertEquals(200, $client->getResponse()->getStatusCode());
        $this->assertContains('Hello ', $crawler->filter('h1')->text());
    }

    function testRandomNameGenerator(){
        $rdName = HelloController::generateRandomName();
        $this->assertGreaterThan(1, strlen($rdName));
    }

}
```


**Exécuter** les tests :
```bash
php vendor/bin/phpunit tests
```

On devrait avoir 3 tests avec 5 assertions :
```
OK (3 tests, 5 assertions)
```


On peut bien sur voire fonctionner ce nouveau contrôleur avec la route : [http://localhost:8000/helloRandom](http://localhost:8000/helloRandom) et recharger la page plusieurs fois...

<div class="question">

### Questions  6 et 7

Dans la classe de test `HelloControllerTest` :

- expliquer ce que fait la méthode `testHelloRandomRoute`
- expliquer ce que fait la méthode `testRandomNameGenerator`
</div>

##  Route et action paramétriques

On souhaite créer une action qui dépende des paramètres de la route (une route paramétrique).

Ajouter la méthode suivante à la classe `HelloController` :

```php
/**
 * @Route("/hello/{name}", defaults={"name" = ""})
 */
public function nameAction($name){
    if($name == ""){
        $name = self::generateRandomName();
    }
    return new Response("<html><body><h1>Hello $name</h1></body></html>");
}
```

**Tester** cette méthode [http://localhost:8000/hello/Albus Perceval Wulfric Brian Dumbledore](http://localhost:8000/hello/Albus Perceval Wulfric Brian Dumbledore)

<div class="question">

### Questions  8

Ecrire un test dans la classe `HelloControllerTest` qui vérifie que la méthode fonctionne comme prévu. Vérifier que la page web générée salut bien le Professeur Dumbledore quand son nom est passé dans l'URL. Vérifier aussi que le nom est aléatoire quand aucun paramètre n'est donné ([http://localhost:8000/hello](http://localhost:8000/hello)).

Coller votre (ou vos) méthode(s) de test dans le questionnaire Eureka (Question 8).
</div>

## Création d'une vue

On souhaite maintenant générer des pages Web complètes et pas seulement une chaine de caractères passée dans l'objet `Response`.

Symfony utilise le langage de _template_ `twig`.

la méthode `render()` du contrôleur permet d'utiliser un template `twig` pour fabriquer une réponse et retourner un objet `Response`.


**Ouvrir** et **lire** les fichiers `app/Resources/views/base.html.twig` et  `app/Resources/views/default/index.html.twig`.


Créer un template pour notre contrôleur Hello `app/Resources/views/default/hello.html.twig` :

{% raw %}
```html
{% extends 'base.html.twig' %}

{% block title %}Hello!{% endblock  %}

{% block body %}
    <div id="container">
        <div id="hello">
            <h1> Hello <i>{{ name }}</i>!</h1>
        </div>

    </div>
{% endblock %}

{% block stylesheets %}
<style>
    body { background: #F5F5F5; font: 18px/1.5 sans-serif; }
    h1, h2 { line-height: 1.2; margin: 0 0 .5em; }
    h1 { font-size: 36px; }
    #container { background: #FFF; margin: 1em auto; max-width: 800px; width: 98%; padding: 2em; box-sizing: border-box;}

    @media (min-width: 768px) {
        #container {  margin: 2em auto; }
    }
</style>
{% endblock %}
```
{% endraw %}

<div class="question">
### Questions  9
En vous inspirant du contrôleur `DefaultController` modifier l'action `nameAction` de notre contrôleur `HelloController` afin d'utiliser le _template_ qui vient d'être créé.

Copier le code de cette action dans la question 9 du questionnaire Eureka.
</div>

## Sessions

On souhaite que l'application se souvienne de nous. A chaque fois que l'action `nameAction` est appelée avec un paramètre (avec un nom), on veut qu'il soit **sauvegardé** dans une **session** (en remplaçant éventuellement celui qui existait déjà). A chaque fois que l'action `nameAction` est appelée **sans** paramètre, alors on veut retrouver le paramètre précédemment **enregistré**. Finalement si `nameAction` est appelé sans paramètre pour la première fois alors on génère un nom aléatoirement.

Par exemple, si j'appelle une fois ([http://localhost:8000/hello/You](http://localhost:8000/hello/You)) cela va m'afficher `"Hello You!"`. Si ensuite j'appelle (dans le même navigateur) ([http://localhost:8000/hello](http://localhost:8000/hello)) cela doit également m'afficher `"Hello You!"`. En revanche si on appelle pour la première fois l'action sans paramètres ([http://localhost:8000/hello](http://localhost:8000/hello)), alors on obtient un nom aléatoire qui va être stocké pour les appels futurs.


<div class="question">

### Questions  10 et 11

En utilisant la [documentation Symfony sur les sessions](http://symfony.com/doc/current/book/controller.html#managing-the-session) :

- Modifier l'action `nameAction` pour qu'elle se comporte comme décrit au dessus. Copier le code dans Eureka (Question 10).  
- Ecrire des tests pour vérifier le nouveau fonctionnement. Copier le code de ces tests dans Eureka (Question 11).  
</div>

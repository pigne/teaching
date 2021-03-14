---
layout: post
title: TP Framework CSS
categories:  
- InfoWeb
- lab
published: true

---

## Les Frameworks CSS 

Pour créer des pages Web de qualité rapidement, on peut s'aider d'outils comme les Frameworks CSS qui facilitent la génération de contenu web et donnent rapidement un rendu visuel acceptable.

Un framework Web nous donne principalement accès à deux types de  fonctionnalités :

1. il permet la création rapide et facile de composants graphiques élaborés (bares de navigation, panneaux, grilles, boites, listes, onglets, ...)
2. il respecte un style graphique qui unifie tous les éléments visuels de la page en respectant un style donné (un *styleguide* ou *Design Language*).

Les *styleguides* sont publiés par différents acteurs du Web. Parmi les dizaines de guides qui existent on peut citer :

- [Material Design](https://material.io/design/introduction/#principles) par Google
- [IBM Design Language](https://www.ibm.com/design/language/)
- [Microsoft Design guidelines](https://developer.microsoft.com/en-us/windows/apps/design)
- [iOS Design Guidelines](https://ivomynttinen.com/blog/ios-design-guidelines)
- ...

Il existe un bon nombre de *Frameworks CSS*. Parmi eux :

- <https://purecss.io> (par yahoo)
- <https://materializecss.com> (thème :  Material Design de Google)
- <https://www.muicss.com> (thème : Material Design)
- <https://getbootstrap.com> (thèmes réglables)
- <https://bulma.io> 
- <https://foundation.zurb.com> 
- <https://www.knacss.com> (alsacréation)

## Design de pages Web *Responsive*

Un site web dont le design est *responsive* propose d'adapter visuellement l'affichage en fonction du support d'affichage (l'écran).
On n'écrit pas plusieurs versions du site en fonction du support mais on inclue dans le code source (HTML et CSS) les règles d'affichages conditionnelles en fonction du support. En CSS les [media querries](https://en.wikipedia.org/wiki/Media_queries) permettent d'avoir du style conditionnel.

## Le Framework Bulma

### Installation

Dans sa version la plus simple, Bulma est un simple fichier CSS. Il suffit donc de l'inclure dans une page html pour l'utiliser. 

Créer un dossier de travail ( `TP-Framework-CSS` par exemple) et y ajouter un fichier `index.html` avec ce contenu minimal : 

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Hello Bulma!</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bulma@0.9.1/css/bulma.min.css">
  </head>
  <body>
  <section class="section">
    <div class="container">
      <h1 class="title">
        Hello World
      </h1>
      <p class="subtitle">
        My first website with <strong>Bulma</strong>!
      </p>
    </div>
  </section>
  </body>
</html>
```

### Documentation

Se référer à la [documentation de bulma](https://bulma.io/documentation/) pour obtenir un panel de toutes les fonctionnalités du Framework.

## Travail à réaliser

On se propose de créer un site Web en HTML et CSS statique (pas de PHP) qui permet de présenter les équipes et les poules de sélection du championnat du monde de hockey sur glace 2020. Le but est de réaliser plusieurs pages sur ce sujet en utilisant le Framework CSS Bulma. Ce site possède plusieurs pages ou ensemble de pages liées entre elles. Le design doit respecter celui proposé par Bulma. Un maximum de [composants](https://bulma.io/documentation/components/) et d'[éléments](https://bulma.io/documentation/elements/) graphiques doivent être utilisés. Les pages sont "*responsives*" et utilisent le système de [colonnes](https://bulma.io/documentation/columns/) du Bulma.

Toutes les pages ont la même base graphique avec une entête contenant un logo et un titre. Le logo devient centré et le tite passe sur la seconde ligne en mode étroit (mobile). Chaque page propose un menu latéral qui disparaît en mode étroit (mobile).

### La page des groupes

La page des groupes présente tous les groupes avec le nom et drapeau de chaque équipe sur une seule page. Deux groupes  par ligne sont affichés, en mode large (desktop) et  un groupe par ligne en mode étroit (mobile).

La page des groupes en mode large :

![Groupes mode large]({{ site.baseurl }}/images/L3-INFOWEB-TPCSS-Groupes-Large.png){:.image-width-90}

La page des groupes en mode étroit :

![Groupes mode étroit]({{ site.baseurl }}/images/L3-INFOWEB-TPCSS-Groupes-Etroit.png){:.image-width-50}

### Une page par groupe

Chaque groupe a sa propre page sous forme d'un onglet d'un composant `tab`. Dans chaque page figure à minima un tableau  des matchs avec le nom des équipes, la date et le lieux des matchs.

Exemple d'une page du groupe, à minima, en mode desktop :  
![Groupe A mode étroit]({{ site.baseurl }}/images/L3-INFOWEB-TPCSS-GroupeA-Large.png){:.image-width-90}

Pas la peine d'écrire *toutes* les pages de groupes. Une seule suffit. Pas la peine d'afficher *tous* les matchs d'un groupe. Une dizaine suffit.

### Les pages équipe

A vous d'imaginer ce qu'une page équipe peut afficher en utilisant les éléments et composants de Bulma. Vous pouvez vous inspirer de  Wikipedia (e.g. [Équipe du Canada de Hockey](https://en.wikipedia.org/wiki/Canada_men%27s_national_ice_hockey_team)) pour constater qu'une équipe possède un emblème, des couleurs, un sélectionneur, un capitaine, un classement internationale, etc. Pas la peine d'écrire *toutes* les pages équipes. Une seule suffit.

<!-- ## Fin du TP

Dans le terminal qui exécutait la commande `npm start` arrêter le script avec <kbd>Ctrl+c</kbd>. Puis créer une archive du tp en **excluant** le dossier `node_modules`. Si le dossier s'appelle `L3_TP_InfoWeb_CSS`:

```sh
DOSSIER="TP-Framework-CSS"
tar --exclude="${DOSSIER}/node_modules" -zcvf "${DOSSIER}.tar.gz" "${DOSSIER}"
```

Déposer l'archive sur Eureka. -->

## Pour aller plus loin


Bulma nous permet d'obtenir des pages Web stylisées très rapidement. Mais bulma est configurable à volonté. On peu en effet changer le thème graphique, les tailles les polices, les couleurs, etc. Pour cela il ne faut plus utiliser le fichier CSS pré-compilé de Bulma. Il faut modifier/configurer et compiler le fichier source au format SASS : un langage de description puissant qui permet de produire du CSS. 




#### nodejs

Pour développer facilement il faut disposer de l'interpréteur javascript nodejs. On commence donc par installer node :
  
  - Windows : <https://nodejs.org/fr/download/>
  - ubuntu : 
    ```sh
    curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
    sudo apt-get install -y nodejs
    ```
On vérifie que tout s'est bien passé en tapant la commande `node -v`.

#### Bulma Start

On utilise le projet **Bulma Start** pour obtenir un projet de base fonctionnel. 

On se place dans un dossier de travail dans lequel on va créer le projet : 

```sh
cd dossier/de/travail # c'est vous qui décidez
```

On télécharge le projet depuis github, puis on retire l'historique GIT :

```sh

git clone https://github.com/jgthms/bulma-start TP-Framework-CSS

cd TP-Framework-CSS

rm -rf .git/
```

On peut maintenant installer Bulma Start : 

```sh
npm install # oui ça prend du temps...
npm start # ça bloque le termial, c'est normal
```

Ce terminal est bloqué, c'est normal, on le laisser ouvert.

Ouvrir un explorateur de fichier dans le dossier `TP-Framework-CSS` pour inspecter la structure du projet : 



 et ouvrir un éditeur de texte pour voir les fichiers et un navigateur qui pointe sur le fichier `index.html`.




### SASS /  CSS

Le code CSS n'est pas directement utilisé lors du développement. On utilise un langage intermédiaire (ici, `SASS`) qui va être compilé en `CSS` à la volée. Quand on modifie le fichier `_sass/main.scss` le fichier `css/main.css` est automatiquement régénéré. Il ne faut donc **pas modifier directement le fichier `css/main.css`** car il sera écrasé.  Le format de fichier `scss` de `SASS` permet d'écrire du code `CSS` classique et de profité des avantages de `SASS` (variables, mixins, etc.)

L'utilisation de `SASS` permet de modifier les variables définies par Bulma. Ces variables sont définies dans les fichiers  `node_modules/bulma/sass/utilities/initial-variables.sass` et `node_modules/bulma/sass/utilities/derived-variables.sass`. On peut les redéfinir pour personnaliser le thème visuel de nos pages. Dans `_sass/main.scss` on va commencer par modifier ces variables et en premier lieux les couleurs de base : 

```scss
// 1. Choisissez vos propre couleurs
$purple: #8A4D76;
$pink: #FA7C91;
$brown: #757763;
$beige-light: #D0D1CD;
$beige-lighter: #EFF0EB;

// 2. modifier les variables de Bulma par défaut
$primary: $purple;
$grey-dark: $brown;
$grey-light: $beige-light;
$link: $pink;
$widescreen-enabled: false;
$fullhd-enabled: false;

// 4. modifier plus précisément les valeurs par défaut du thème
$body-background-color: $beige-lighter;
$control-border-width: 2px;
$input-border-color: transparent;
$input-shadow: none;

// 5. Import the rest of Bulma

@import "../node_modules/bulma/bulma";

```


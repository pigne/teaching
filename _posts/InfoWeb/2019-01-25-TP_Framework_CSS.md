---
layout: post
title: TP Framework CSS
categories:  
- InfoWeb
- lab
published: true
update: 2020-01-26
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

Télécharger l'archive [Bulma Start](https://bulma.io/bulma-start/) en cliquant sur le bouton download.

Extraire l'archive. Renommer (e.g.  `L3_TP_InfoWeb_CSS/` )et déplacer le dossier selon vos besoins, puis ouvrir un terminal dans ce dossier et taper les commandes : 

```bash
cd L3_TP_InfoWeb_CSS/ # le nom de votre dossier
npm install
npm start
```

Ce terminal est bloqué, le laisser ouvert et ouvrir un éditeur de texte pour voir les fichiers et un navigateur qui point sur le fichier `index.html`.




### SASS /  CSS

Le code CSS n'est pas directement utilisé lors du développement. On utilise un langage intermédiaire (ici, `SASS`) qui va être compilé en `CSS` à la volée. Quand on modifie le fichier `_sass/main.scss` le fichier `css/main.css` est automatiquement régénéré. Il ne faut donc pas modifier directemenr le fichier `css/main.css` car il sera écrasé.  Le format de fichier `scss` de `SASS` permet d'écrire du code `CSS` classique et de profité des avantages de `SASS` (variables, mixins, etc.)

L'utilisation de `SASS` permet de modifier les variables définies par Bulma. Ces variables sont définies dans les fichiers  `node_modules/bulma/sass/utilities/initial-variables.sass` et `node_modules/bulma/sass/utilities/derived-variables.sass`. On peut le redéfinir pour personnaliser le thème visuel de nos pages. Dans `_sass/main.scss` on va commencer par modifier ces variables et en premier lieux les couleurs de base : 

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

### HTML

Le fichier `index.html` est un exemple de base que l'on modifie et que l'on copie pour commencer toute autre page html.

### Documentation

Se référer à la [documentation de bulma](https://bulma.io/documentation/) pour obtenir un panel de toutes les fonctionnalités du Framework.

## Travail à réaliser

On se propose de créer un site Web en HTML et CSS statique (pas de PHP) qui permet de présenter les équipes et les poules de sélection du championnat du monde de hockey sur glace 2020. Le but est de réaliser plusieurs pages sur ce sujet en utilisant le Framework CSS Bulma. Ce site possède plusieurs pages ou ensemble de pages liées entre elles. Le design doit respecter celui proposé par Bulma. Un maximum de [composants](https://bulma.io/documentation/components/) et d'[éléments](https://bulma.io/documentation/elements/) graphiques doivent être utilisés. Les pages sont "*responsives*" et utilisent le système de [colonnes](https://bulma.io/documentation/columns/) du Bulma.

Toutes les pages ont la même base graphique avec une entête contenant un logo et un titre. Le logo devient centré et le tite passe sur la seconde ligne en mode étroit (mobile). Chaque page propose un menu latéral qui disparaît en mode étroit (mobile).

### La page des groupes

La page des groupes présente tous les groupes avec le nom et drapeau de chaque équipe sur une seule page. Trois groupes  par ligne sont affichés, en mode large (desktop) et  deux groupes par ligne en mode étroit (mobile).

La page des groupes en mode large :

![Groupes mode large]({{ site.baseurl }}/images/L3-INFOWEB-TPCSS-Groupes-Large.png){:.image-width-90}

La page des groupes en mode étroit :

![Groupes mode étroit]({{ site.baseurl }}/images/L3-INFOWEB-TPCSS-Groupes-Etroit.png){:.image-width-50}

### Une page par groupe

Chaque groupe a sa propre page sous forme d'un onglet d'un composant `tab`. Dans chaque page figure à minima un tableau  des matchs avec le nom des équipes, la date et le lieux des matchs.

Exemple d'une page du groupe, à minima, en mode étroit :  
![Groupe A mode étroit]({{ site.baseurl }}/images/L3-INFOWEB-TPCSS-GroupeA-Etroit.png){:.image-width-90}

Pas la peine d'écrire *toutes* les pages de groupes. Une seule suffit. Pas la peine d'afficher *tous* les matchs d'un groupe. Une dizaine suffit.

### Les pages équipe

A vous d'imaginer ce qu'une page équipe peut afficher en utilisant les éléments et composants de Bulma. Vous pouvez vous inspirer de  Wikipedia (e.g. [Équipe du Canada de Hockey](https://en.wikipedia.org/wiki/Canada_men%27s_national_ice_hockey_team)) pour constater qu'une équipe possède un emblème, des couleurs, un sélectionneur, un capitaine, un classement internationale, etc. Pas la peine d'écrire *toutes* les pages équipes. Une seule suffit.

## Fin du TP

Dans le terminal qui exécutait la commande `npm start` arrêter le script avec <kbd>Ctrl c</kbd>. Puis créer une archive du tp. Si le dossier s'appelle `L3_TP_InfoWeb_CSS`:

```sh
DOSSIER="L3_TP_InfoWeb_CSS"
tar --exclude="${DOSSIER}/node_modules" -zcvf "${DOSSIER}.tar.gz" "${DOSSIER}"
```

Déposer l'archive sur Eureka.
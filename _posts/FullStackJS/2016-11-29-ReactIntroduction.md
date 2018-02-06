---
layout: post
title: Introcution à React
categories:
- FullStackJS
- lecture
author: Yoann Pigné
published: true
last_modified_at: 02-12-2017
---

## Les bases de React

- Bibliothèque JavaScript de manipulation et composition de composants
- Permet la création d'interfaces graphiques.
- Utilise un langage de template de type XML (JSX)
- Chaque composant possède des propriétés et *peut* posséder un état.


### le langage JSX

Ce langage peut s'écrire directement dans le code javascript si l'on traduit le javascript avant de l'exécuter. C'est le cas aussi pour ES6/ES7. Le traducteur (*transpiler*) `babel` sait également traduire le JSX.

Les expressions JSX ne sont **pas des `String` ni des balises HTML**.

[Exemple minimal](http://codepen.io/gaearon/pen/ZpvBNJ?editors=0010) :

```js
ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
);
```




### Les composants

La classe de base de *React* est `React.Component`. On étend cette classe pour créer de nouveaux composants.

Chaque composant a une méthode `render` qui retourne un objet `JSX`.

```js
class Greetings extends React.Component {
  render() {
    return (
      <div>
        <h1>Greetings M. or Ms. {this.props.name}</h1>  
      </div>
    );
  }
}
```

L'intérêt principal est la composition de composants.

[Exemple de composition](http://codepen.io/pigne/pen/XNeRXO?editors=0012)  :

```js
class N extends React.Component {
  render() {
    return (
      <ul>
        <li> Les Entier Naturels </li>
      </ul>
    )
  }
}
class Z extends React.Component {
  render() {
    return (
      <ul>
        <li>Les Entier Relatifs  <N/> </li>
      </ul>)
  }
}
class Q extends React.Component {
  render() {
    return (
      <ul>
        <li> Les Nombres Rationnels  <Z/> </li>
      </ul>
    )
  }
}
class R extends React.Component {
  render() {
    return (
      <ul>
        <li> Les Nombres réels <Q/> </li>
      </ul>
    )
  }
}

ReactDOM.render(
  <R />,
  document.getElementById('root')
);
```

Chaque composant de doit retourner qu'un seul élément racine (`<ul>`dans l'exemple). On ajoute un `<div>` quand nécessaire.


### ReactDOM

Les composants React sont des objet javascript simple jusqu'à ce qu'ils soient transformés en DOM par `ReactDOM`.

La création, la composition et la mise a jour des composants ne fait pas directement intervenir le DOM.

Lors d'une mise à jours, seuls les élément qui diffèrent sont mis à jour dans le DOM.


[Exemple de mise à jours du site React](http://codepen.io/gaearon/pen/gwoJZk?editors=0010) (inspecter le DOM)









### Propriétés des composants


Les composant peuvent utiliser des propriétés passées en attribut dans le code JSX.


[Exemple de composant avec propriétés](http://codepen.io/pigne/pen/LbzyBN?editors=0010) :

```js
class FullName extends React.Component {
  render() {
    return (
      <span>
        {this.props.first+ ' '+ this.props.last}
      </span>
    )
  }
}

ReactDOM.render(
  <FullName first="Jason" last="Bourne" />,
  document.getElementById('root')
);
```



### Les classes dans JSX

On peut spécifier un attribut `class` dans les éléments DOM générés. Du fait que `class` soit un mot réservé du langage on utilise l'attribut `className`

[Exemple de manipulation de `className`](http://codepen.io/pigne/pen/qqPjjj?editors=0010);




### Ecriture fonctionnelle

Quand les composant n'ont pas d'état interne, on peut les écrire sous forme de fonction JavaScript simples. Elles sont équivalentes aux classes dans ce cas.

```js
function FullName2(props) {
  return (<span>{props.first}  {props.last}</span>);
}

const FullName3 = ({first, last}) => (
  <span className="glyphicon glyphicon-user" >{first}  {last}</span>
);
```

Les propriétés (`props`) ne doivent pas être modifiées. Ces fonction sont dites *pures* car elle ne modifient l'état de leurs paramètres d'entrée et possèdent un comportement déterministe étant donné ces paramètres. Pour un ensemble de paramètres donnés en entré, une fonction pure retourne toujours le même résultat.


### Etats

On donne aux composant un état interne avec la propriété `state`. Il faut le créer et l'initialiser dans le constructeur.

On modifie le `state`  uniquement via la fonction `setState` en fournissant :

- soit une fonction *updater* qui prend l'état précédent et les propriétés actuelles du composant et retourne un nouvel état ;
- soit un objet qui sera fusionné avec l'état précédent pour former un nouvel état. 

<https://reactjs.org/docs/react-component.html#setstate>


[Exemple de manipulation d'état](http://codepen.io/pigne/pen/rWGwvM?editors=0010).


## Template de projet 

Les différentes étape de configuration pour permettre de réellement développer un projet React sont un peu fastidieuses. On va utiliser ici un projet qui met en place une configuration fonctionnelle.

Installer le projet suivant et suivre les instruction pour commencer un nouveau projet :

<https://github.com/facebookincubator/create-react-app>



## Lab interactif

Suivre le [premier tutoriel](https://facebook.github.io/react/tutorial/tutorial.html) sur le site de React en utilisant `create-react-app`.




---
layout: post
title: Introcution à Redux
categories:
- FullStackJS
- lecture
author: Yoann Pigné
---


Redux (<http://redux.js.org/>) est une bibliothèque JS permettant de gérer l'état d'une application de manière déterministe.

Redux propose un conteneur (le ***store***) dont les modifications sont décrites par des ***actions*** (sortes d'évènements) qui sont gérés par des réducteur (***reducers***).


## Le Réducteur (*reducer*)

Un réducteur (*reducer*) est une fonction pure, sans état, qui prend en paramètre un état et une action,  pour retourner un (nouvel) état:

```
(état, action) => état
```

Décrit  comment une *action* va modifier un *état* donner pour retourner un *nouvel état*.

Le nouvel état, retourné par la fonction, est un **nouvel** objet. L'état d'origine n'est **pas modifié**.

Cette fonction est dite *pure* car :

- elle est déterministe (si on l'appelle 10 fois avec les mêmes paramètres, elle renvoie 10 fois le même résultat) ;
- elle ne modifie pas ses paramètres ;
- elle ne modifie aucune autre ressource externe (pas de fermeture).


```js

// fonction pure
const plus = (a,b) => {
  return a+b;
}

// fonction pure
const increment = (a) => {
  return a+1;
}


// fonction impure (effet de bord)
let toto=0;
const effetDeBord(a){
  toto = a;
}

// fonction impure (modifie les paramètres)
let etat = {valeur:1}
const modifieEtat = (etat) => {
  etat.valeur++;
  return etat;
}

// fonction impure (modifie les paramètres)
const etat = {valeur:1}
const nouvelEtat = (etat) => {
  return Object.assign({}, etat, {valeur:etat.valeur+1} );
}
```

## les Actions

C'est un simple objet JS qui a pour seul contrainte d'avoir une propriété `type` sérialisable (e.g. une `string`) assi que n'importe quelle autre propriété permettant au réducteur de générer un nouvel état.

```js
const action = {
  type: 'CHANGE_MENU_SELECTION',
  selectedMenuItem: '#about_menu'
}
```

## Le Store Redux

Le *store Redux* est l'objet javascript qui contient l'état **immuable** d'une application.

Toute modification du *store* doit passer par un *reducer* qui va générer un nouvel état.

On crée un *store* avec la fonction `createStore`  et en paramètre un reducteur capable de gérer les modifications.

```js
function counterReducer(state = 0, action) {
  switch (action.type) {
  case 'INCREMENT':
    return state + 1
  case 'DECREMENT':
    return state - 1
  default:
    return state
  }
}

let store = createStore(counterReducer)
```

L'objet store possède trois méthodes :

- `subscribe` qui permet a tout écouteur (*listener*) d'être notifié en cas de modification du *store*. Les gestionnaires de vues  (comme *React*) vont souscrire au *store* pour être notifié des modification et effectuer mettre à jour l'interface graphique en conséquence.

- `dispach` qui prend en paramètre une *action* et exécute le *reducer* qui va, à son tour, mettre à jour le store avec un nouvel état.

- `getState` qui retourne l'état courant du store. L'objet retourné ne doit pas être modifié.

```js
store.subscribe(() =>
  console.log(store.getState())
)

store.dispatch({ type: 'INCREMENT' })
// 1
store.dispatch({ type: 'INCREMENT' })
// 2
store.dispatch({ type: 'DECREMENT' })
// 1
```


[Exemple Redux de base sur CodePen.](http://codepen.io/pigne/pen/KNxBwO?editors=0012)

## Inclure le store dans React

Pour donner accès au store au différents composants d'une application React, Redux offre le composant `Provider`.

```js
import React from 'react'
import { render } from 'react-dom'
import { createStore } from 'redux'
import { Provider } from 'react-redux'
import App from './components/App'
import reducer from './reducers'

const store = createStore(reducer)

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```


Pour qu'un composant React ai effectivement accès au store il faut "connecter" ce dernier au store.

On utilise la fonction [`connect`](https://github.com/reactjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options) de Redux sur le composant désiré pour lui donner accès.

Les options de la fonction `connect`sont nombreuses. On peut vouloir avoir accès à la fonction `dispatch` uniquement ou bien s'enregistrer pour recevoir les modifications du store. Le filtrage est possible.  

```js

import React from 'react'
import { connect } from 'react-redux'

let stuffIndex=0;
const addStuffAction = (stuff) => ({
  id: sruffIndex++,
  stuff
});

let AddStuff = ({ dispatch }) => {
  let input

  return (
    <div>
      <form onSubmit={e => {
        e.preventDefault()
        if (!input.value.trim()) {
          return
        }
        dispatch(addTodo(input.value))
        input.value = ''
      }}>
        <input ref={node => {
          input = node
        }} />
        <button type="submit">
          Add Stuff
        </button>
      </form>
    </div>
  )
}

export default connect()(AddStuff)
```



## Combiner les reducteurs

La fonction `createStore` ne prend en parametre q'un seul réducteur qui va être chargé gérer toutes les actions de l'application. Or plusieurs types d'actions différentes vont cohabiter.

Par exemple, les actions liées à la modification du model peuvent êtres séparées des actions liées à l'interface graphique.

On va combiner les réducteur avec la fonction `combineReducers`.


### Exemple

On stocke 2 types d'informations dans le store :

- Un tableau d'objets (`stuff`) qui représente le modèle,
- Une propriété graphique (`display`) qui permet de savoir comment afficher les objets (en liste, en grille, en miniatures...)


```js
const stuff = (state, action) => {
  switch(action.type){
    'ADD_STUFF': return [
        ...state,
        {action.id, action.stuff}
      ]
    'REMOVE_STUFF': return state.filter((s)=>(s.id !== action.id))
    default: return state
  }
}

const display = (state, action) => {
  switch(action.type){
    'CHANGE_DISPLAY': return action.displayType
    default: 'DISPLAY_LIST'
  }
}

const reducer = combineReducers({
  stuff,
  display
})

const initialState = {
  stuff: [
    { id:1, stuff:'OK'},
    { id:2, stuff:'KO'}
  ],
  display: 'DISPALY_LIST'
}

const store = createStore(reducer, initialState)
```



## Exemples Redux

On peut cloner le repo redux et étudier les exemples.

`https://github.com/reactjs/redux.git` ou
`git@github.com:reactjs/redux.git`

Puis on peut explorer l'exemple TodoApp.

```bash
cd redux/examples/todos
npm i
npm start
```


## Gestions des actions asynchrones


`Redux Thunk middleware` est un module redux qui permet d'écrire des fonctions de création d'actions qui retournent une fonction au lieu de retourner une action.

Cette fonction retournée reçoit les méthodes `dispatch` et `getState` su store en paramètre.

Ce mécanisme permet de retarder l'exécution du dispatch d'une action. Ce mécanisme est utile lors de l'utilisation de code asynchrone comme un appel a `fetch`.

On configure d'abord le store avec le middleware

```js
import { createStore, applyMiddleware } from 'redux';
import thunkMiddleware from 'redux-thunk';

const reducer = /* ... le réducer de l'application ... */

const etatInitial = {/* ...*/}

const store = createStore(
    reducer,
    etatInitial,
    applyMiddleware(
      thunkMiddleware
    )
  );
}
```


On peut ensuite l'utiliser dans les créateurs d'actions

```js
function fetchStuff(subreddit) {
  return function (dispatch) {
    dispatch(requestStuff(lol))
    return fetch(`https://www.example.com/${lol}`)
      .then(response => response.json())
      .then(json => dispatch(receiveStuff(lol, json))
      ).catch(err => dispatch(cancelStuff(err)));

      // In a real world app, you also want to
      // catch any error in the network call.
  }
}
```

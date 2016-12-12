---
layout: post
title: Introcution à React Router
categories:
- FullStackJS
- lecture
author: Yoann Pigné
---

[`React Router`](https://github.com/ReactTraining/react-router) est une extension a React qui permet de gérer les routes d'une application coté client. Il permet de synchroniser (d'associer ) des composants graphiques `React` à des urls.

Traditionnellement c'est la partie `hash` (`#`) de l'URL qui peut être manipulée par le client grâce à la propriété `location` : `window.location.hash`

```
http://example.com/chemin/serveur/#/url/cote/client
```

Mais la norme HTML5 apporte une API permettant la manipulation de l'historique du navigateur : l'[API *history*](https://developer.mozilla.org/en-US/docs/Web/API/History_API). L'interface  `window.history` permet de manipuler l'historique et de réécrire les URL sans déclencher le chargement de celle-ci sur le serveur.


Sans React Router, la synchronisation des URL avec les composants React est possible mais fastidieuse. Un composant doit écouter les modifications d'URL grâce aux fonctionnalités de base du navigateur et adapter le rendu graphique en conséquence.

## Surcharge des objets `location` et `history`.

React Router permet de manipuler des 2 interfaces natives `location` et `history` pour les intégrer dans ses composants.

- support de `location`: `hashHistory`
- support de `history` (HTML5): `browserHistory`


## Des composants `React`

Les objets proposés par *React Router* sont de véritables composants `React`.

2 composants logiques :

- `Router` Ce composant englobe toutes les toutes de l'application. C'est à lui qu'est confié le gestionnaire d'historique.
- `Route` Ce composant d'finit une route précise. Il associe a une URL (qui peut être paramétrique) un composant React associé

```js
import { Router, Route, Link, browserHistory } from 'react-router'

...

<Router history={browserHistory}>,
  <Route path="/" component={App}>
  <Route path="/about" component={About}>
  <Route path="/contact" component={Contact}>
</Router>
```

Un composant graphique :

- `Link` Ce composant produit une balise de type ancre `<a>...</a>` avec la particularité que celle-ci sait si l'URL courante est la sienne (il sait s'il est actif ou pas). On utilise la propriété `to` pour donner la cible.

On utilise la propriété `activeStyle` pour spécifier un style en ligne quand le lien est actif :

```js
const style = { color: 'red' };
// ...
<Link to="/about" activeStyle={style}>About</Link>
```

On peut également utiliser des classes :

```html
<Link to="/about" activeClassName="active">About</Link>
```

## Des routes paramétriques

Les routes spécifiées dans les composant `Route` peuvent être paramétriques. Ces paramètres peuvent se retrouver dans le composant appelés via la propriété `this.props.param`


```js

// ...

<Route path="/albums/:albumId/tracks/:trackId" component={Track} />

// ...

export class Track extends React.Component {
  render() {
    return (
      <div>
        <h2>Track #{this.props.params.trackId} ... </h2>
      </div>
    )
  }
}
```

## Imbriquer les routes

L'intérêt des frameworks JS coté client est d'avoir une web app dans une seule page et de mettre a jour seulement des parties de la page.

Les composants React peuvent être imbriquer comme l'est le DOM. Il en va de même pour les routes. La propriété `children` d'un composant React permet de représenter ses sous composants.  Ainsi un composant parent peut définir ou vont se positionner des descendants.

On va imbriquer les composants `Route` pour spécifier des sous-routes.


Par exemple une application affichant une liste d'albums qui contiennent eux-même une liste de chansons, peuvent utiliser l'arborescence de vues suivante :




```
/albums/
/albums/1234
/albums/1234/tracks
/albums/1234/tracks/12
```


```js
<Route path="/albums" component={Albums}>
  <Route path="/albums/:albumId/" component={Album}>
    <Route path="/albums/:albumId/tracks" component={Tracks}>
      <Route path="/albums/:albumId/tracks/:trackId" component={Track} />
    </Route>
  </Route>
</Route>
```


## Vue par défaut

Lors de l'utilisation des sous-composants il est possible d'afficher un composant par défaut si aucun sous-composant n'est sélectionné. On utilise le composant `IndexRoute`.

Dans l'exemple précédent, quand aucun album n'est sélectionné, le composant `Albums` correspondant à l'URL `"/albums/"` voudra afficher un contenu par défaut dans l'espace réservé a son sous-composant `Album`.

```js
<Route path="/albums" component={Albums}>
  <IndexRoute component={AlbumsIndex}/>
  <Route path="/albums/:albumId/" component={Album}>
  ...
```

## Lien dynamiques par programmation

Grâce aux URL paramétriques et à l'interface `history` il est possible de faire changer l'URL par programmation, après un évènement autre qu'un clic sur une lien.

Pour accéder au router à partir d'un composant utiliser le composant [withRouter](https://github.com/ReactTraining/react-router/blob/master/upgrade-guides/v2.4.0.md#withrouter-hoc-higher-order-component) :



```js
import { withRouter } from 'react-router'
// ...

class MonComposant extends  React.Component {
  // ...
  someEvent() : {
    const path = createSomeNewURL();
    this.props.router.push(path);
  }
  // ...
}

export default withRouter(MonComposant)
```

## Rendre les URL coté serveur

L'idée de React Router est que le rendu de l'application se fait progressivement coté client. On commence la navigation à la racine de l'application, puis a force d'interaction l'application évolue et les URL aussi qui se composent au fur et à mesure.

Or peut être amené de vouloir rendre directement depuis le serveur une URL composée, si on recharge la page, si on partage un lien, ou pour l'indexation.

Il faut que le serveur soit capable de faire le rendu. React Permet de faire le rendu d'un composant dans une `string`  plutôt que dans une arbre DOM.

```js
ReactDOMServer.renderToString(element)
```


## Tutorial React Router

Suivre le [Tutorial React Router](https://github.com/reactjs/react-router-tutorial).

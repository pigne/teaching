---
layout: post
title: Introduction à React Router
categories:
- WebDev1
- lecture
author: Yoann Pigné
published: true
---

[`React Router`](https://github.com/ReactTraining/react-router) est une extension a React qui permet de gérer les routes d'une application coté client. Il permet de synchroniser (d'associer) des composants graphiques `React` à des urls.

La norme HTML5 apporte une API permettant la manipulation de l'historique du navigateur : l'[API *history*](https://developer.mozilla.org/en-US/docs/Web/API/History_API). L'interface  `window.history` permet de manipuler l'historique et de réécrire les URL sans déclencher le chargement de celle-ci sur le serveur. 

*React Router* tire profit de l'interface `window.history` pour manipuler les url et les associer à des composants React.

*React Router*, comme `window.history`, s'intéresse à la partie à droite du nom de domaine dans l'URL. 

Ainsi dans l'url `https://som.example.com/this/and/that`, le chemin géré par *React Router* est `/this/and/that`.


Sans React Router, la synchronisation des URL avec les composants React est possible mais fastidieuse. Un composant doit écouter les modifications d'URL grâce aux fonctionnalités de base du navigateur et adapter le rendu graphique en conséquence.

## installation


```bash
npm install --save react-router-dom
```

## Surcharge de l'objet  `history` du navigateur: le composant `BrowserRouter`

Ce composant surcharge l'interface `History` du navigateur et rend possible l'association de routes a des composants. C'est un véritable composant *React*.

```jsx
import { BrowserRouter } from 'react-router-dom'

ReactDOM.render((
  <BrowserRouter>
    <App />
  </BrowserRouter>
), document.getElementById('root'));
```

## Le composant `<Route>`

Le composant `<Route>` est l'élément principale de *React Router*. On doit utiliser un composant `<Route>` partout  où le rendu est basé sur l'url du navigateur.

## Les paramètres du composant `<Route>`

### gérer les chemins avec le paramètre `path`

Le composant `<Route>` attend un paramètre `path` qui définit à quel chemin (partie droite de quelle url, après le nom de domaine) cette route est associée. Quand le chemin dans le navigateur correspond au champ `path` du composant, alors cette route est activée.

un `path` peut représenter une partie d'un littéral : 

```jsx
<Route  path='/truc' />
```

Ici, les chemins `/truc`, `/truc/machin/42`, seront reconnues  mais pas `/`.

Pour reconnaître uniquement `/truc` on utilise le paramètre `exact` :

```jsx
<Route  exact path='/truc' />
```

### Comportement en cas d'activation: `component` et `render`

Une fois la `<Route>` activée grâce à se paramètre  `path` la route va activer le rendu d'un composant. On utilise soit le nom d'un composant existant (si on n'a pas de paramètre à lui confier) avec le paramètre `component`:

```jsx
<Route  path='/truc'  component={Truc}/>
```

Si des paramètres doivent être passés au composant on peut géfinir celui-ci dynamiquement  avec le paramètre `render`. `render` prend en paramètre une fonction considérée comme un composant :

```jsx
<Route  path='/truc'  render={(props) => (
  <Truc {...props} propsTruc={extraProps}/>
)}/>
```

## configurer plusieurs routes avec `<Switch>`

```jsx
import { Switch, Route } from 'react-router-dom'

const Main = () => (
  <main>
    <Switch>
      <Route exact path='/' component={Home}/>
      <Route path='/truc' component={Truc}/>
      <Route path='/machin' component={Machin}/>
    </Switch>
  </main>
)
```

## Routes imbriqués

On peut définir dynamiquement et n'importe ou dans l'application les routes. Celles-ci sont automatiquement imbriquées. 

Ainsi dans le composant `<Truc>` routé précédemment on peut a nouveau utiliser des `<Route>` :

```jsx
import { Route } from 'react-router-dom'

const Truc = () => (
  /*  ... */
  <Route path={`/truc/un`} component={Un} />
  <Route path={`/truc/deux`} component={Deux} />
  <Route exact path='/truc' render={() => <h3>Truc général</h3>} />
```

## Routes paramétriques

Permet d'utiliser des expressions rationnelles dans le `path` et de récupérer l'information dans les composants grâce au paramètre `match`.

Exemple from <https://reacttraining.com/react-router/web/example/basic> :

```jsx
const Topics = ({ match }) => (
  <div>
    <h2>Topics</h2>
    <Route path={`${match.path}/:topicId`} component={Topic} />
    <Route
      exact
      path={match.path}
      render={() => <h3>Please select a topic.</h3>}
    />
  </div>
);

const Topic = ({ match }) => (
  <div>
    <h3>{match.params.topicId}</h3>
  </div>
);
```

## Un composant graphique pour faire des liens

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

## Vue par défaut

Lors de l'utilisation des sous-composants il est possible d'afficher un composant par défaut si aucun sous-composant n'est sélectionné. On utilise le composant `IndexRoute`.

Dans l'exemple précédent, quand aucun album n'est sélectionné, le composant `Albums` correspondant à l'URL `"/albums/"` voudra afficher un contenu par défaut dans l'espace réservé a son sous-composant `Album`.

```xml
<Route path="/albums" component={Albums}>
  <IndexRoute component={AlbumsIndex} />
  <Route path="/albums/:albumId/" component={Album}>
  <!-- ... -->
```

## redirections

```jsx
import { Route, Redirect } from 'react-router'

<Route exact path="/" render={() => (
  loggedIn ? (
    <Redirect to="/dashboard"/>
  ) : (
    <PublicHomePage/>
  )
)}/>
```

## Rendre les URL coté serveur

L'idée de React Router est que le rendu de l'application se fait progressivement coté client. On commence la navigation à la racine de l'application, puis a force d'interaction l'application évolue et les URL aussi qui se composent au fur et à mesure.

On peut être amené de vouloir rendre directement depuis le serveur une URL composée, si on recharge la page, si on partage un lien, ou pour l'indexation.

Il faut que le serveur soit capable de faire le rendu. React Permet de faire le rendu d'un composant dans une `string`  plutôt que dans une arbre DOM.

```js
ReactDOMServer.renderToString(element)
```

On peut résoudre les routes coté serveur avec `<StaticRouter>` : <https://reacttraining.com/react-router/web/api/StaticRouter>

```jsx
const html = ReactDOMServer.renderToString(
  <StaticRouter location={req.url} context={context}>
    <App />
  </StaticRouter>
);
```

## Get Started

On peut partir de cet exemple minimal : <https://reacttraining.com/react-router/web/guides/quick-start>

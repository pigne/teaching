---
layout: post
title: Les Fondamentaux de JavaScript Moderne
categories:
- WebDev1
- lecture
author: Yoann Pigné
published: true
---



### Syntaxe ES6+

#### Let, const et scoping

En ES6, `let` et `const` remplacent `var` pour améliorer la gestion des variables.

```js
let x = 10;
const y = 20;
```

- `let` permet de définir une variable dont la portée est limitée au bloc courant.
- `const` est utilisé pour les variables dont la valeur ne sera pas réassignée.

:warning: **Attention à la différence entre** réassignation et modification d’objet. `const` empêche la réassignation, mais les propriétés d’un objet déclaré avec `const` restent modifiables.

```js
const obj = { name: 'Alice' };
obj.name = 'Bob'; // Pas d'erreur, seule la référence reste constante.
```

Privilégier `const` chaque fois que possible.

#### Fonctions fléchées

Les **fonctions fléchées** (arrow functions) simplifient la syntaxe et capturent le `this` lexicalement.

```js
const add = (a, b) => a + b;
```

Différences par rapport aux fonctions classiques :
- Pas de liaison propre au mot-clé `this`.
- Pas de `arguments` implicite.



---


### Template Literals

Les **template literals** introduits avec ES6 permettent d’incorporer des expressions dans des chaînes de caractères de manière plus concise que la concaténation classique.

```js
let name = 'Alice';
let greeting = `Bonjour, ${name}!`; // Bonjour, Alice!
```

- Utilise des backticks (`` ` ``) au lieu de guillemets simples ou doubles.
- Les expressions s’insèrent avec `${expression}`.

:bulb: Les sauts de ligne sont conservés dans les chaînes de caractères.

```js
let message = `Ceci est
une chaîne multilignes.`;
```


---


### Destructuration et Opérateur Spread/Rest

#### Destructuration

La **destructuration** permet d’extraire des valeurs d’objets ou de tableaux en une seule ligne.

```js
const person = { name: 'Alice', age: 25 };
const { name, age } = person;
console.log(name); // Alice
```

Pour les tableaux :

```js
const numbers = [1, 2, 3];
const [first, second] = numbers;
```

#### Spread (`...`)

L’opérateur **spread** permet de décomposer un tableau ou un objet.

```js
const arr1 = [1, 2];
const arr2 = [...arr1, 3]; // [1, 2, 3]
```

#### Rest (`...`)

L’opérateur **rest** collecte des arguments restants.

```js
function sum(...numbers) {
  return numbers.reduce((acc, n) => acc + n, 0);
}
```

`spread` et `rest` simplifient la manipulation des données complexes.



---



### Types et Structures de Données

JavaScript est un langage dynamique, ce qui signifie qu'il n'y a pas de types statiques prédéfinis. Cela permet une grande flexibilité, mais nécessite également une gestion des types et des structures de données efficace.

#### Type dynamique vs TypeScript

JavaScript est un langage à typage dynamique, ce qui signifie que les types des variables sont déterminés au moment de l'exécution. En revanche, **TypeScript** introduit un système de types statiques où les types des variables, des fonctions et des objets sont définis au moment de la compilation.

- TypeScript offre des avantages pour la détection précoce des erreurs liées aux types, et peut être intégré progressivement dans un projet JavaScript existant.
  
#### Tableaux, objets, et Map/Set

**Tableaux (Array)** et **Objets** sont les deux structures de données fondamentales en JavaScript.

- **Tableaux (Array)** : utilisés pour stocker des listes ordonnées d'éléments. Par défaut, les tableaux JavaScript peuvent contenir des éléments de types différents.
  
  ```js
  let arr = [1, 'Alice', true];
  ```

- **Objets (Object)** : utilisés pour représenter des collections de paires clé-valeur. Les clés sont des chaînes ou des symboles, et les valeurs peuvent être de n'importe quel type.

  ```js
  let person = { name: 'Alice', age: 25 };
  ```

Les **Map** et **Set** sont des structures de données plus récentes et plus puissantes, introduites avec ES6, offrant des fonctionnalités améliorées par rapport aux objets classiques.

- **Map** : collection clé-valeur où les clés peuvent être de n'importe quel type, contrairement aux objets où les clés sont des chaînes ou des symboles. De plus, les **Map** conservent l’ordre d’insertion des paires clé-valeur.

  ```js
  let map = new Map();
  map.set('nom', 'Alice');
  map.set(1, 'valeur numérique');
  ```

- **Set** : collection d’éléments uniques. Il ne permet pas de duplication d’éléments et est souvent utilisé pour stocker des valeurs distinctes.

  ```js
  let set = new Set();
  set.add(1);
  set.add(2);
  set.add(1); // Ne sera pas ajouté
  console.log(set); // Set { 1, 2 }
  ```

#### WeakMap et WeakSet

Les **WeakMap** et **WeakSet** sont similaires aux **Map** et **Set**, mais avec des différences importantes : elles ne conservent pas de références fortes aux objets qu'elles contiennent. Cela signifie que si un objet est collecté par le ramasse-miettes (garbage collector), il est automatiquement supprimé de la collection.

- **WeakMap** : une collection de paires clé-valeur où les clés sont des objets et les valeurs peuvent être de n'importe quel type.

  ```js
  let weakmap = new WeakMap();
  let obj = {};
  weakmap.set(obj, 'valeur associée');
  ```

- **WeakSet** : une collection d’objets uniques, où les éléments sont des objets et la collection ne maintient pas de référence forte.

  ```js
  let weakset = new WeakSet();
  let obj = {};
  weakset.add(obj);
  ```

#### Manipulation des structures via les méthodes modernes (`map`, `filter`, `reduce`)

JavaScript moderne propose plusieurs méthodes de manipulation des tableaux qui permettent d’écrire du code plus fonctionnel et déclaratif.

- **`map()`** : crée un nouveau tableau avec les résultats de l’application d’une fonction à chaque élément du tableau.

  ```js
  let numbers = [1, 2, 3];
  let doubled = numbers.map(n => n * 2);
  console.log(doubled); // [2, 4, 6]
  ```

- **`filter()`** : crée un nouveau tableau avec tous les éléments qui passent un test défini dans une fonction.

  ```js
  let numbers = [1, 2, 3, 4];
  let even = numbers.filter(n => n % 2 === 0);
  console.log(even); // [2, 4]
  ```

- **`reduce()`** : applique une fonction à un accumulateur et à chaque élément d'un tableau pour obtenir une seule valeur (comme une somme, un produit, ou un objet).

  ```js
  let numbers = [1, 2, 3];
  let sum = numbers.reduce((acc, n) => acc + n, 0);
  console.log(sum); // 6
  ```

---


### Programmation Asynchrone

La programmation asynchrone est essentielle pour écrire des applications JavaScript réactives et efficaces. En raison de son modèle basé sur un seul fil d'exécution, JavaScript utilise des mécanismes asynchrones pour éviter de bloquer l'exécution du code tout en attendons des réponses longues (comme des requêtes réseau, des opérations sur des fichiers, etc.).

#### Promesses et async/await

Une **promesse (Promise)** est un objet représentant l'achèvement (ou l'échec) d'une opération asynchrone. Elle permet de chaîner des opérations asynchrones et de gérer les erreurs plus efficacement.

- Une promesse peut être dans un des trois états suivants :
  - **pending** (en attente)
  - **fulfilled** (réalisée)
  - **rejected** (échouée)

```js
let myPromise = new Promise((resolve, reject) => {
  let success = true;
  if(success) {
    resolve('Opération réussie');
  } else {
    reject('Opération échouée');
  }
});

myPromise.then(result => {
  console.log(result); // "Opération réussie"
}).catch(error => {
  console.log(error); // "Opération échouée"
});
```

**`async/await`** introduits avec ES2017 (ES8) sont une syntaxe plus simple pour travailler avec des promesses.

- **`async`** marque une fonction comme asynchrone.
- **`await`** permet de "mettre en pause" l'exécution d'une fonction asynchrone jusqu'à ce que la promesse soit résolue ou rejetée.

```js
async function fetchData() {
  try {
    let response = await fetch('https://api.example.com/data');
    let data = await response.json();
    console.log(data);
  } catch (error) {
    console.error('Erreur:', error);
  }
}
```

#### Différences entre callbacks et Promises

Les **callbacks** sont la méthode traditionnelle pour gérer des opérations asynchrones. Cependant, les callbacks peuvent entraîner des problèmes comme le *callback hell* ou pyramide de l’enfer, où les callbacks imbriqués deviennent difficiles à gérer.

```js
// Callback hell
doSomething((err, result) => {
  if (err) {
    console.error(err);
  } else {
    doSomethingElse(result, (err, result2) => {
      if (err) {
        console.error(err);
      } else {
        doThirdThing(result2, (err, result3) => {
          if (err) {
            console.error(err);
          } else {
            console.log(result3);
          }
        });
      }
    });
  }
});
```

Les **promesses** et **async/await** offrent une meilleure gestion des erreurs et permettent d'éviter le code imbriqué, rendant l’asynchrone plus lisible et facile à maintenir.

#### API Fetch et gestion des erreurs

La **fonction `fetch`** est une API moderne pour effectuer des requêtes HTTP de manière asynchrone. Elle renvoie une promesse, ce qui permet de chaîner `.then()` pour traiter la réponse.

```js
fetch('https://api.example.com/data')
  .then(response => {
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    return response.json();
  })
  .then(data => console.log(data))
  .catch(error => console.error('Il y a eu un problème avec la requête Fetch :', error));
```

Utiliser **async/await** avec `fetch` permet de rendre le code encore plus lisible.

```js
async function getData() {
  try {
    const response = await fetch('https://api.example.com/data');
    if (!response.ok) throw new Error('Erreur réseau');
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error('Erreur de récupération des données :', error);
  }
}
```

#### Chaining de Promesses

Une des grandes puissances des promesses est le **chaînage**. En utilisant `.then()`, on peut exécuter plusieurs actions de manière séquentielle.

```js
fetch('https://api.example.com/data')
  .then(response => response.json())
  .then(data => {
    console.log('Données récupérées:', data);
    return fetch(`https://api.example.com/details/${data.id}`);
  })
  .then(response => response.json())
  .then(details => console.log('Détails récupérés:', details))
  .catch(error => console.error('Erreur:', error));
```

#### Parallelisme avec `Promise.all`

Parfois, on souhaite exécuter plusieurs tâches asynchrones en parallèle. **`Promise.all()`** permet de gérer plusieurs promesses à la fois et d'attendre que toutes soient résolues.

```js
let promise1 = fetch('https://api.example.com/data1');
let promise2 = fetch('https://api.example.com/data2');

Promise.all([promise1, promise2])
  .then(responses => Promise.all(responses.map(response => response.json())))
  .then(data => {
    console.log('Données des deux requêtes:', data);
  })
  .catch(error => console.error('Erreur:', error));
```

---


### Concepts Avancés

Une fois que les bases du langage sont maîtrisées, il est important de comprendre certains des concepts avancés qui rendent JavaScript puissant et flexible. Ces concepts permettent de gérer la portée, l’héritage, les comportements asynchrones et les modèles de conception.

#### Closures et portée lexicale

Une **closure** (fermeture) est une fonction qui se souvient de son environnement lexical, même lorsqu’elle est exécutée en dehors de ce contexte. Cela signifie qu’une fonction interne peut accéder aux variables de la fonction externe qui l’a créée, même après que celle-ci a terminé son exécution.

```js
function outer() {
  let count = 0;
  return function inner() {
    count++;
    console.log(count);
  }
}

let increment = outer();
increment(); // 1
increment(); // 2
```

Cela permet de créer des **data encapsulation** ou des variables privées dans un contexte où JavaScript ne supporte pas directement la notion de visibilité des membres d’une classe.

#### Modules ES (import/export)

Les **modules ES** sont un moyen de structurer et organiser le code JavaScript en morceaux réutilisables. ES6 introduit les mots-clés `import` et `export` pour permettre l'importation et l'exportation de blocs de code, ce qui facilite la gestion de projets JavaScript plus complexes.

- **`export`** : permet d'exporter une variable, une fonction, une classe, etc., pour la rendre accessible dans d'autres fichiers.

```js
// math.js
export function add(a, b) {
  return a + b;
}
export const pi = 3.14;
```

- **`import`** : permet d'importer ces éléments depuis un autre fichier.

```js
// main.js
import { add, pi } from './math.js';
console.log(add(2, 3)); // 5
console.log(pi); // 3.14
```

#### Design Patterns simples en JS

Les **design patterns** (ou patrons de conception) sont des solutions éprouvées à des problèmes récurrents. En JavaScript, les patrons sont souvent utilisés pour structurer des applications ou gérer des comportements réutilisables.

- **Singleton** : un patron où une classe n’a qu’une seule instance, partagée dans toute l’application.

```js
class Singleton {
  constructor() {
    if (!Singleton.instance) {
      Singleton.instance = this;
    }
    return Singleton.instance;
  }
}

const instance1 = new Singleton();
const instance2 = new Singleton();
console.log(instance1 === instance2); // true
```

- **Factory** : un patron qui permet de créer des objets sans exposer le processus de création.

```js
class Animal {
  constructor(name) {
    this.name = name;
  }
}

class AnimalFactory {
  static createAnimal(type, name) {
    switch (type) {
      case 'dog':
        return new Animal(name);
      case 'cat':
        return new Animal(name);
      default:
        throw new Error('Unknown animal type');
    }
  }
}

let dog = AnimalFactory.createAnimal('dog', 'Fido');
console.log(dog.name); // Fido
```

- **Observer** : un patron où un objet (l'observateur) se "registre" pour être notifié lorsqu'un autre objet (le sujet) subit un changement d'état.

```js
class Subject {
  constructor() {
    this.observers = [];
  }

  addObserver(observer) {
    this.observers.push(observer);
  }

  notify() {
    this.observers.forEach(observer => observer.update());
  }
}

class Observer {
  update() {
    console.log('L\'état du sujet a changé');
  }
}

let subject = new Subject();
let observer = new Observer();

subject.addObserver(observer);
subject.notify(); // L'état du sujet a changé
```

#### Symboles

**Symbol** est un type primitif introduit en ES6. Les symboles sont uniques et immuables, souvent utilisés pour ajouter des propriétés aux objets de manière à éviter les collisions de noms.

```js
const sym = Symbol('description');
let obj = {
  [sym]: 'valeur unique'
};
console.log(obj[sym]); // valeur unique
```

#### Opérateur Nullish Coalescing (`??`)

L'opérateur **Nullish Coalescing** (`??`) permet de retourner la première valeur non nulle ou définie parmi deux valeurs. Il est similaire à l'opérateur **OR (`||`)**, mais diffère en ce qu'il ne remplace pas une valeur si celle-ci est **0** ou **false**.

```js
let x = null;
let y = 5;

console.log(x ?? y); // 5

let a = 0;
let b = 5;
console.log(a ?? b); // 0 (ici, l'opérateur ?? ne remplace pas 0, contrairement à ||)
```

#### Optional Chaining (`?.`)

L’opérateur **Optional Chaining** (`?.`) permet d'accéder à une propriété d'un objet profondément imbriqué sans avoir à vérifier si chaque niveau existe. Si une référence est **null** ou **undefined**, l'expression renverra `undefined` au lieu de provoquer une erreur.

```js
let person = { name: { firstName: 'John' } };

console.log(person?.name?.firstName); // John
console.log(person?.address?.city); // undefined
```

#### Boucles modernes (`for...of`, `for...in`)

JavaScript propose plusieurs types de boucles pour itérer sur des collections.

- **`for...of`** : permet d’itérer directement sur les valeurs d’une collection (idéal pour les tableaux, strings, etc.).

```js
let arr = [1, 2, 3];
for (let value of arr) {
  console.log(value); // 1, 2, 3
}
```

- **`for...in`** : permet d’itérer sur les clés d’un objet ou les indices d’un tableau.

```js
let person = { name: 'Alice', age: 25 };
for (let key in person) {
  console.log(key, person[key]); // name Alice, age 25
}
```

#### Gestion avancée des erreurs avec `try...catch`

Le bloc **`try...catch`** est essentiel pour gérer les erreurs de manière robuste, en capturant les exceptions et en les traitant de manière appropriée.

```js
try {
  let result = riskyOperation();
  console.log(result);
} catch (error) {
  console.error('Une erreur est survenue :', error);
} finally {
  console.log('Le bloc try/catch est terminé');
}
```

---








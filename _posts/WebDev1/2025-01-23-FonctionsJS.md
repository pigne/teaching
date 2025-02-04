---
layout: post
title: Fonctions en JavaScript
categories:
- WebDev1
- lecture
author: Yoann Pigné
published: true 
update: 2025-02-02
---



## Généralités

- Les fonctions sont des objets.
- Elles sont liées à `Function.prototype` (lui-même lié à `Object.prototype`).
- Elles possèdent une propriété `prototype`, distincte du lien de prototype.
- Cette propriété est utilisée par les fonctions constructrices pour définir le prototype des objets créés.
- Toutes les fonctions possèdent un lien vers l'objet qui les invoque via `this`.
- Elles ont accès aux paramètres passés grâce à `arguments` (similaire à un tableau, mais obsolète en ES6+).

## Paramètres des fonctions

En JavaScript, le nombre de paramètres déclarés dans une fonction n'est pas strictement imposé lors de son appel. Les paramètres manquants sont `undefined` et les arguments supplémentaires sont ignorés à moins d'utiliser l'opérateur rest (`...args`).

```js
function afficherArguments(...args) {
    args.forEach((arg, i) => console.log(`${i} => ${arg}`));
}
afficherArguments('ok', false);
// 0 => ok
// 1 => false
```

L'ordre des arguments peut être difficile à gérer. Une bonne pratique consiste à utiliser un objet d'options :

```js
const obj = new Objet({
    width: w,
    height: h,
    color: c,
    opacity: o,
    shadow: s
});
```

## Fonctions en tant que méthodes

Lorsque des fonctions appartiennent à un objet et sont invoquées par celui-ci, elles deviennent des **méthodes**. Dans ce contexte, `this` fait référence à l'objet propriétaire.

```js
const point = {
    x: 10,
    y: 10,
    toString() {
        return `(${this.x}, ${this.y})`;
    }
};
console.log(point.toString()); // '(10, 10)'
```

## Fonctions utilisées seules

Lorsqu'elles sont appelées sans objet propriétaire, les fonctions se lient à l'objet global (`window` en navigateur, `globalThis` en ES2020).

```js
globalThis.nom = 'Global';
function afficherNom() {
    console.log(this.nom);
}
afficherNom(); // 'Global'
```

## Fonctions constructrices

Les fonctions utilisées avec `new` sont appelées **fonctions constructrices**. Elles créent de nouveaux objets et leur assignent `this`.

```js
class Point {
    constructor(x = 0, y = 0) {
        this.x = x;
        this.y = y;
    }
    toString() {
        return `(${this.x}, ${this.y})`;
    }
}
const p = new Point();
```

## Invocation avec un contexte différent

JavaScript permet d'invoquer des fonctions avec un contexte spécifique via `call`, `apply` et `bind`.

```js
const weirdo = { x: 'α', y: 'β' };
console.log(Point.prototype.toString.call(weirdo)); // '(α, β)'
console.log(Point.prototype.toString.apply(weirdo)); // '(α, β)'
const f = Point.prototype.toString.bind(weirdo);
console.log(f()); // '(α, β)'
```

## Fonctions immédiatement invoquées (IIFE)

Les IIFE permettent d'éviter de polluer l'espace global.

```js
(() => {
    let a = 0;
    console.log(a); // 0
})();
console.log(typeof a); // undefined
```

## Fonctions fléchées

- Syntaxe plus courte, idéale pour les fonctions anonymes.
- Ne possède pas de `this`, `arguments`, `super` ni `new.target`.

```js
const calcul = x => x * x;
console.log(calcul(2)); // 4
```

Les fonctions fléchées peuvent aussi être utilisées comme fermetures :

```js
function createCounter() {
    let count = 0;
    return () => ++count;
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
```

Ici, la fonction fléchée conserve l'accès à la variable `count`, démontrant ainsi le concept de fermeture.





### Quand utiliser `this` dans une fonction fléchée ?

✅ **Utiliser `this` dans une fonction fléchée quand :**
- Vous avez un **callback dans un objet** et voulez éviter la perte du contexte (`setTimeout`, `setInterval`, etc.).
  ```js
  const user = {
    name: "Alice",
    greet() {
        setTimeout(() => {
        console.log(`Bonjour, je suis ${this.name}`);
        }, 1000);
    }
  };
  user.greet(); // ✅ Affiche "Bonjour, je suis Alice" après 1s
    ```
- Vous gérez des **événements DOM** sans vouloir que `this` soit l'élément HTML.
  ```js
    const myObj = {
    name: "Bouton magique",
    handleClick() {
        document.querySelector("button").addEventListener("click", () => {
        console.log(`${this.name} a été cliqué !`);
        });
    }
    };

    myObj.handleClick(); 
    // ✅ Affiche "Bouton magique a été cliqué !" au clic sur le bouton
    ```
- Vous définissez des **méthodes dans une classe** et voulez éviter `.bind(this)`.
  ```js
  class Person {
    constructor(name) {
        this.name = name;
    }

    greet() {
        setTimeout(() => {
        console.log(`Salut, je suis ${this.name}`);
        }, 1000);
    }
    }

    const p = new Person("Bob");
    p.greet(); // ✅ Affiche "Salut, je suis Bob" après 1s
    ```
- Vous passez une **fonction anonyme à `map`, `forEach`, etc.**, et voulez garder `this`.
  ```js
  const team = {
    players: ["Alice", "Bob", "Charlie"],
    showPlayers() {
        this.players.forEach(player => {
        console.log(`${this.name} a ${player} dans l'équipe`);
        });
    }
    };

    team.name = "Les Champions";
    team.showPlayers();
    // ✅ Affiche :
    // "Les Champions a Alice dans l'équipe"
    // "Les Champions a Bob dans l'équipe"
    // "Les Champions a Charlie dans l'équipe"
    ```

❌ **Ne PAS utiliser `this` dans une fonction fléchée quand :**
- Vous définissez une **méthode d'objet** (`greet: () => {}` → Ne pas faire ça !).
- Vous créez une **fonction constructeur** (`() => {}` ne peut pas être utilisé avec `new`).

### 🎯 **Règle simple à retenir** :
> **Si vous avez besoin d'un `this` dynamique (lié à l'objet qui appelle la méthode), utilisez une fonction classique. Si vous voulez un `this` fixe (lié au contexte où la fonction est définie), utilisez une fonction fléchée.** 🚀





## Fonctions génératrices

Une fonction génératrice retourne un itérateur et peut être mise en pause grâce à `yield`.

```js
function* fib(max) {
    let [a, b] = [1, 1];
    while (a < max) {
        yield a;
        [a, b] = [b, a + b];
    }
}
for (const val of fib(100)) {
    console.log(val);
}
```

## Mode strict

Le mode strict empêche certaines erreurs silencieuses et interdit des structures dangereuses.

```js
'use strict';

function test() {
    let x = 17;
    // delete x; // TypeError
}
```

## Exceptions

Les exceptions interrompent un programme anormalement. Utilisation de `try/catch` pour les gérer.

```js
function Point(x, y) {
    if (typeof x !== 'number' || typeof y !== 'number') {
        throw new TypeError("'Point' attend des nombres");
    }
}
try {
    new Point('α', 0);
} catch (e) {
    console.log(e.message);
}
```

## Récursivité

```js
function fib(n) {
    return n <= 2 ? 1 : fib(n - 1) + fib(n - 2);
}
```

Optimisation avec appel terminal :

```js
function fib(n, a = 1, b = 1) {
    return n < 2 ? a : fib(n - 1, b, a + b);
}
```

## Fermetures (Closures)

Les fonctions conservent l'accès aux variables de leur contexte de création.

```js
function compteur() {
    let count = 0;
    return () => ++count;
}
const incrementer = compteur();
console.log(incrementer()); // 1
console.log(incrementer()); // 2
```

## Héritage avec `class`

```js
class Point3D extends Point {
    constructor(x, y, z = 0) {
        super(x, y);
        this.z = z;
    }
    toString() {
        return `(${this.x}, ${this.y}, ${this.z})`;
    }
}
const p3d = new Point3D(1, 2, 3);
console.log(p3d.toString()); // '(1, 2, 3)'
```

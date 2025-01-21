---
layout: post
title: Introduction à TypeScript
categories:
- WebDev1
- lecture
author: Yoann Pigné
published: true
---


TypeScript est un sur-ensemble de JavaScript qui ajoute un système de typage statique et des fonctionnalités orientées objet. Cela permet de capturer des erreurs avant l'exécution, d'améliorer la lisibilité du code et de bénéficier de l'auto-complétion dans les IDEs. En utilisant TypeScript, on garde la flexibilité de JavaScript tout en bénéficiant de la puissance d'un typage statique.

### Concepts de Base

#### Types primaires et annotations

TypeScript permet d'ajouter des annotations de type sur les variables, fonctions, et autres éléments. Cela permet d’éviter les erreurs courantes liées aux types et de rendre le code plus lisible.

Voici quelques exemples :

```ts
let age: number = 25;
let name: string = 'Alice';
let isActive: boolean = true;
```

**`any`** : si vous ne voulez pas spécifier un type particulier, vous pouvez utiliser le type `any`. Cependant, cela enlève une grande partie des avantages de TypeScript, car tout devient permis.

```ts
let anything: any = 'Hello';
anything = 42; // Aucun problème
```

#### Interfaces et types customisés

Les **interfaces** permettent de décrire des objets complexes, spécifiant les propriétés et les méthodes qu'un objet doit posséder. Elles permettent aussi d’assurer une structure solide du code.

```ts
interface Person {
  name: string;
  age: number;
  greet(): void;
}

let john: Person = {
  name: 'John',
  age: 30,
  greet: function () {
    console.log(`Hello, my name is ${this.name}`);
  }
};
```

**`type`** : TypeScript offre aussi un alias de type avec `type`, ce qui permet d'exprimer des types plus complexes, y compris les unions et intersections.

```ts
type ID = string | number;

let userId: ID = '123';
userId = 456; // Ok
```

#### Types avancés : union, intersection, optionnel

- **Union types** : permet de définir un type qui peut être l'un de plusieurs types.

```ts
let id: string | number;
id = '123'; // Ok
id = 456; // Ok
id = true; // Erreur
```

- **Intersection types** : combine plusieurs types en un seul type.

```ts
interface User {
  name: string;
  age: number;
}

interface Admin {
  privileges: string[];
}

type AdminUser = User & Admin;

const admin: AdminUser = {
  name: 'Alice',
  age: 28,
  privileges: ['manage-users', 'edit-settings']
};
```

- **Optionnal types** : les propriétés peuvent être marquées comme optionnelles avec le suffixe `?`.

```ts
interface Car {
  brand: string;
  model?: string; // Propriété optionnelle
}

let myCar: Car = { brand: 'Toyota' }; // ok
```

### Classes et Modèles Objet

Les **classes** en TypeScript sont très similaires à celles de JavaScript, mais elles sont dotées de fonctionnalités supplémentaires grâce au système de typage statique.

#### Classes et héritage

Les classes permettent de créer des objets avec des comportements et des propriétés définies. TypeScript introduit des types et des modificateurs d'accès pour mieux contrôler l'accès aux propriétés et méthodes.

```ts
class Animal {
  constructor(public name: string) {}

  speak(): void {
    console.log(`${this.name} makes a sound`);
  }
}

class Dog extends Animal {
  constructor(name: string, public breed: string) {
    super(name);
  }

  speak(): void {
    console.log(`${this.name} barks`);
  }
}

const dog = new Dog('Max', 'Golden Retriever');
dog.speak(); // Max barks
```

**Modificateurs d'accès** : TypeScript permet de définir des modificateurs d'accès pour les propriétés et méthodes des classes (`public`, `private`, `protected`).

```ts
class Person {
  public name: string;
  private age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
  
  public greet(): void {
    console.log(`Hello, my name is ${this.name}`);
  }
}
```

#### Décorateurs en TypeScript

Les **décorateurs** sont une fonctionnalité avancée de TypeScript (en phase expérimentale) qui permet d'ajouter des métadonnées ou d'altérer des comportements de classes, de méthodes, ou de propriétés.

```ts
function logClass(target: Function) {
  console.log(`Class created: ${target.name}`);
}

@logClass
class Person {
  constructor(public name: string) {}
}

let p = new Person('Alice'); // Affiche "Class created: Person"
```

Les décorateurs sont souvent utilisés dans des frameworks comme Angular pour manipuler des classes et des méthodes.

### Typage dans un Projet Web

L'un des avantages de TypeScript est de pouvoir l'utiliser pour typer les APIs, qu'il s'agisse de fetchers REST ou de travailler avec des bibliothèques comme React et Node.js.

#### Typage des API (Fetch avec Axios + Types)

Quand on effectue des requêtes HTTP, TypeScript peut nous aider à définir des types pour les données que nous récupérons. Par exemple, avec Axios :

```ts
import axios from 'axios';

interface User {
  id: number;
  name: string;
  email: string;
}

async function getUserData(userId: number): Promise<User> {
  const response = await axios.get<User>(`https://api.example.com/users/${userId}`);
  return response.data;
}
```

#### TypeScript dans React/Node.js

Dans un projet **React** avec TypeScript, vous pouvez typer les props, les states, et même les hooks.

```ts
interface ButtonProps {
  label: string;
  onClick: () => void;
}

const Button: React.FC<ButtonProps> = ({ label, onClick }) => (
  <button onClick={onClick}>{label}</button>
);
```

Dans **Node.js**, vous pouvez typer les requêtes HTTP, les environnements, et même les interactions avec les bases de données.

```ts
import express from 'express';

const app = express();

interface RequestBody {
  name: string;
  age: number;
}

app.post('/user', (req, res) => {
  const body: RequestBody = req.body;
  res.json(body);
});
```

---

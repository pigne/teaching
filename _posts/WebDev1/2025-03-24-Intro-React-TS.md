---
layout: post
title: Introduction à React avec TypeScript
categories:
- WebDev1
- lecture
author: Yoann Pigné
published: true
---


## Présentation de React

React est une bibliothèque JavaScript développée par Facebook permettant de concevoir des interfaces utilisateur interactives et dynamiques. Son architecture repose sur des **composants réutilisables**, facilitant ainsi la création et la maintenance d'applications web modernes.

### Avantages de React
- **Modularité** : Organisation du code en composants réutilisables.
- **Performance** : Mise à jour optimisée grâce au Virtual DOM.
- **Écosystème étendu** : Large communauté et nombreuses bibliothèques complémentaires.

---

## Mise en place de React avec TypeScript

Vite est un outil de développement moderne permettant d'initialiser rapidement un projet React avec TypeScript.

```bash
npm create vite@latest my-react-app --template react-ts
cd my-react-app
npm install
npm run dev
```

---


## Introduction à JSX

JSX (JavaScript XML) est une extension de syntaxe permettant d'écrire du code HTML au sein de JavaScript. Il est utilisé pour définir l'interface utilisateur dans les composants React.

### Exemple de JSX
```tsx
const element = <h1>Bonjour, monde !</h1>;
```

> **Remarque** : JSX doit être compilé en JavaScript avant d'être interprété par le navigateur.

---

## Définition d'un composant React

Un **composant** est une fonction retournant une interface utilisateur en JSX.

### Exemple de composant sans TypeScript
```tsx
const Hello = (props) => {
  return <h1>Hello, {props.name}!</h1>;
};
```

### Ajout du typage avec TypeScript
```tsx
interface HelloProps {
  name: string;
}

const Hello: React.FC<HelloProps> = ({ name }) => {
  return <h1>Hello, {name}!</h1>;
};
```

> **Pourquoi utiliser TypeScript ?** Il permet de prévenir les erreurs en définissant précisément les types des **propriétés** et des **états**.

---

## Les Props (Propriétés)

Les **props** permettent de transmettre des données d'un composant parent à un composant enfant.

### Exemple de transmission de props
```tsx
const Greeting: React.FC<{ name: string }> = ({ name }) => {
  return <p>Bonjour, {name} !</p>;
};

const App: React.FC = () => {
  return <Greeting name="Alice" />;
};
```
























## Gestion de l'état avec `useState`

Le **state**   permet de déclarer et de mettre à jour un état local dans un composant.

### Exemple : Un compteur
```tsx
import { useState } from 'react';

const Counter: React.FC = () => {
  const [count, setCount] = useState<number>(0);

  return (
    <div>
      <p>Valeur : {count}</p>
      <button onClick={() => setCount(count + 1)}>Incrémenter</button>
    </div>
  );
};
```

> **Bonnes pratiques** : Il est recommandé de typer `useState` afin d'éviter des erreurs liées aux types de données.

---

## Immuabilité du state

En React, il est important de ne pas modifier directement le **state**, mais d'utiliser des fonctions de mise à jour.

### Mauvaise pratique
```tsx
const [user, setUser] = useState({ name: "Alice", age: 25 });
user.age = 26; // Ne pas modifier directement le state !
```

### Bonne pratique
```tsx
setUser((prevUser) => ({ ...prevUser, age: 26 }));
```

> **Pourquoi ?** React repose sur l'immuabilité pour détecter les changements et optimiser les mises à jour.

---

## Gestion des effets de bord avec `useEffect`

 `useEffect` est utilisé pour effectuer des actions secondaires, telles que des appels API, la gestion d'événements ou des mises à jour de l'UI après un changement d'état.

### Exemple : Affichage d'un message lors du montage du composant
```tsx
import { useEffect } from 'react';

const Message: React.FC = () => {
  useEffect(() => {
      console.log("Composant monté");

      return () => {
        console.log("Composant démonté");
      };
    }, []); // La dépendance vide signifie qu'il ne se déclenche qu'une fois


  return <p>Bienvenue !</p>;
};
```

> **Remarque** : La dépendance `[]` indique que l'effet ne s'exécute qu'une seule fois, au premier rendu.

---

## Structure d’un Projet React Moderne

### Configurer React avec Vite ou Next.js

Les outils modernes comme **Vite** et **Next.js** permettent de configurer rapidement des projets React avec TypeScript.

- **Vite** :
  
  Vite est un outil de développement moderne qui fournit une configuration rapide pour les applications React. Il est plus rapide que **Create React App** et supporte TypeScript nativement.

  Pour créer un projet React avec Vite et TypeScript :
  
  ```bash
  npm create vite@latest my-react-app --template react-ts
  cd my-react-app
  npm install
  npm run dev
  ```

- **Next.js** :
  
  Next.js est un framework React pour le rendu côté serveur (SSR) et la génération de pages statiques. Il offre une configuration prête à l’emploi pour TypeScript.

  Pour créer un projet Next.js avec TypeScript :
  
  ```bash
  npx create-next-app@latest my-next-app --typescript
  cd my-next-app
  npm run dev
  ```

**Conseil** : Choisissez Vite pour des applications plus petites et Next.js pour des applications complexes nécessitant des fonctionnalités comme le SSR ou le SSG (Static Site Generation).

### Organisation des dossiers (Atomic Design)

L’organisation des dossiers dans un projet React est cruciale pour maintenir la scalabilité et la clarté du code. Le modèle **Atomic Design** propose de diviser l’interface en petites unités réutilisables.

- **Structure de base** :

  Voici une structure typique pour un projet React utilisant l’Atomic Design :

  ```
  src/
  ├── assets/           # Images, icônes, etc.
  ├── components/       # Composants atomiques
  │   ├── atoms/        # Plus petites unités (ex: Button, Input)
  │   ├── molecules/    # Composants combinant des atomes (ex: Formulaire)
  │   ├── organisms/    # Composants plus complexes (ex: Header, Footer)
  ├── pages/            # Pages de l'application (ex: Home, About)
  ├── utils/            # Fonctions utilitaires, hooks
  ├── services/         # Services API, gestion de la logique métier
  ```

**Conseil** : Utilisez l'Atomic Design pour créer des composants réutilisables et éviter la duplication de code.

---

## Gestion des Données

### Utilisation de Context API

La **Context API** de React permet de partager des données à travers l’arbre de composants sans avoir à passer les props à chaque niveau intermédiaire.

- **Exemple d’utilisation de Context API** :
  
  Créez un contexte pour gérer l’authentification dans votre application :

  ```tsx
  import React, { createContext, useContext, useState } from 'react';

  interface AuthContextType {
    isAuthenticated: boolean;
    login: () => void;
    logout: () => void;
  }

  const AuthContext = createContext<AuthContextType | undefined>(undefined);

  const AuthProvider: React.FC = ({ children }) => {
    const [isAuthenticated, setIsAuthenticated] = useState(false);

    const login = () => setIsAuthenticated(true);
    const logout = () => setIsAuthenticated(false);

    return (
      <AuthContext.Provider value={ { isAuthenticated, login, logout } }>
        {children}
      </AuthContext.Provider>
    );
  };

  const useAuth = () => {
    const context = useContext(AuthContext);
    if (!context) {
      throw new Error("useAuth must be used within an AuthProvider");
    }
    return context;
  };

  export { AuthProvider, useAuth };
  ```

**Conseil** : Utilisez le Context API pour des valeurs globales comme le thème, l’authentification ou les préférences utilisateur.

#### Introduction à Redux Toolkit ou Zustand

- **Redux Toolkit** :
  
  Redux est une bibliothèque pour gérer l'état global de l'application. Redux Toolkit simplifie l'utilisation de Redux avec une configuration prête à l'emploi.

  - Exemple d’utilisation avec Redux Toolkit :

    ```tsx
    import { createSlice, configureStore } from '@reduxjs/toolkit';

    const counterSlice = createSlice({
      name: 'counter',
      initialState: { value: 0 },
      reducers: {
        increment: state => {
          state.value += 1;
        },
        decrement: state => {
          state.value -= 1;
        },
      },
    });

    const store = configureStore({
      reducer: {
        counter: counterSlice.reducer,
      },
    });

    export const { increment, decrement } = counterSlice.actions;
    export default store;
    ```

  - Puis dans un composant React :

    ```tsx
    import { useDispatch, useSelector } from 'react-redux';
    import { increment, decrement } from './store';

    const Counter: React.FC = () => {
      const dispatch = useDispatch();
      const count = useSelector((state: any) => state.counter.value);

      return (
        <div>
          <p>Count: {count}</p>
          <button onClick={() => dispatch(increment())}>Increment</button>
          <button onClick={() => dispatch(decrement())}>Decrement</button>
        </div>
      );
    };
    ```

**Conseil** : Redux Toolkit simplifie l’utilisation de Redux, surtout lorsqu’il s’agit de la gestion d’état complexe ou de grande échelle.

- **Zustand** :
  
  Zustand est une alternative à Redux qui permet de créer des états globaux de manière simple et légère.

  ```tsx
  import create from 'zustand';

  const useStore = create((set) => ({
    count: 0,
    increment: () => set((state) => ({ count: state.count + 1 })),
  }));

  const Counter: React.FC = () => {
    const { count, increment } = useStore();
    return (
      <div>
        <p>Count: {count}</p>
        <button onClick={increment}>Increment</button>
      </div>
    );
  };
  ```

**Conseil** : **Zustand** est plus léger et plus facile à utiliser que Redux pour des applications plus petites ou des projets avec des exigences moins complexes en gestion d’état.

---

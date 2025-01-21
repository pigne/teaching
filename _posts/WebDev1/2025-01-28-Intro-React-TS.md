---
layout: post
title: Introduction à React avec TypeScript
categories:
- WebDev1
- lecture
author: Yoann Pigné
published: false
---

### Concepts de Base

#### Création de composants fonctionnels avec TypeScript

En React, les **composants fonctionnels** sont utilisés pour décrire l'interface utilisateur. Lorsqu'on utilise TypeScript avec React, il est important de typer les composants et leurs props.

- **Exemple de composant fonctionnel avec TypeScript** :

  ```tsx
  import React from 'react';

  // Définition des props avec une interface
  interface HelloProps {
    name: string;
    age: number;
  }

  // Composant fonctionnel avec typage des props
  const Hello: React.FC<HelloProps> = ({ name, age }) => {
    return <h1>Hello, {name}, you are {age} years old!</h1>;
  };

  export default Hello;
  ```

:six: **Conseil** : Utiliser `React.FC` (ou `React.FunctionComponent`) permet de typer les composants fonctionnels en incluant automatiquement les types pour `children` et `propTypes`.

#### Props et State typés

Les **props** et le **state** d’un composant peuvent être typés pour garantir une meilleure sécurité et éviter les erreurs.

- **Typage des props** :
  
  Comme vu précédemment, les props sont définies à l’aide d'une interface.

- **Typage du state avec `useState`** :
  
  Le hook `useState` permet de déclarer un état local dans un composant fonctionnel. Il est important de spécifier le type de cet état pour éviter les erreurs de type.

  ```tsx
  import React, { useState } from 'react';

  const Counter: React.FC = () => {
    const [count, setCount] = useState<number>(0);

    return (
      <div>
        <p>Count: {count}</p>
        <button onClick={() => setCount(count + 1)}>Increment</button>
      </div>
    );
  };

  export default Counter;
  ```

:six: **Conseil** : Le type générique de `useState<T>` permet de définir facilement le type du state, ce qui est particulièrement utile lorsque vous gérez des données complexes.

#### Hooks fondamentaux (`useState`, `useEffect`)

- **`useState`** :
  
  Le hook `useState` permet de déclarer et de mettre à jour un état local dans un composant fonctionnel.

  ```tsx
  const [count, setCount] = useState<number>(0); // Typage explicite
  ```

- **`useEffect`** :
  
  `useEffect` est utilisé pour effectuer des actions secondaires, telles que des appels API, la gestion d'événements ou des mises à jour de l'UI après un changement d'état.

  ```tsx
  import React, { useEffect } from 'react';

  const Component: React.FC = () => {
    useEffect(() => {
      console.log("Composant monté");

      return () => {
        console.log("Composant démonté");
      };
    }, []); // La dépendance vide signifie qu'il ne se déclenche qu'une fois

    return <div>Hello World!</div>;
  };

  export default Component;
  ```

:six: **Conseil** : Utilisez `useEffect` pour la logique qui nécessite de se déclencher en fonction du cycle de vie du composant (montée, mise à jour, démontée).

---

### Structure d’un Projet React Moderne

#### Configurer React avec Vite ou Next.js

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

:six: **Conseil** : Choisissez Vite pour des applications plus petites et Next.js pour des applications complexes nécessitant des fonctionnalités comme le SSR ou le SSG (Static Site Generation).

#### Organisation des dossiers (Atomic Design)

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

:six: **Conseil** : Utilisez l'Atomic Design pour créer des composants réutilisables et éviter la duplication de code.

---

### Gestion des Données

#### Utilisation de Context API

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
      <AuthContext.Provider value={{ isAuthenticated, login, logout }}>
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

:six: **Conseil** : Utilisez le Context API pour des valeurs globales comme le thème, l’authentification ou les préférences utilisateur.

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

:six: **Conseil** : Redux Toolkit simplifie l’utilisation de Redux, surtout lorsqu’il s’agit de la gestion d’état complexe ou de grande échelle.

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

:six: **Conseil** : **Zustand** est plus léger et plus facile à utiliser que Redux pour des applications plus petites ou des projets avec des exigences moins complexes en gestion d’état.

---

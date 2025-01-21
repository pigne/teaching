---
layout: post
title: React Router
categories:
- WebDev1
- lecture
author: Yoann Pigné
published: false
---


### Concepts de Base

**React Router** est une bibliothèque permettant de gérer la navigation dans une application React, en créant des liens dynamiques, en gérant l'historique de navigation et en permettant la gestion des URL pour le rendu conditionnel de composants. 

#### Installation

Pour installer React Router v6 dans ton projet React avec TypeScript, tu peux utiliser `npm` ou `yarn` :

```bash
npm install react-router-dom@6
# ou
yarn add react-router-dom@6
```

#### Composants principaux

- **`BrowserRouter`** : Le composant de haut niveau qui permet de gérer la navigation côté client en utilisant l'API du navigateur.
  
  ```tsx
  import { BrowserRouter } from 'react-router-dom';
  
  const App: React.FC = () => {
    return (
      <BrowserRouter>
        {/* Ton contenu ici */}
      </BrowserRouter>
    );
  };
  ```

- **`Routes`** : Conteneur de toutes les routes. Contrairement à `Route` qui était utilisé directement dans React Router v5, avec la version v6, **`Routes`** remplace l’ancien usage de `Switch`.
  
  ```tsx
  import { Routes, Route } from 'react-router-dom';

  const App: React.FC = () => {
    return (
      <BrowserRouter>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
        </Routes>
      </BrowserRouter>
    );
  };
  ```

- **`Route`** : Définit une route qui associe une URL à un composant spécifique via la prop `element`.

  ```tsx
  <Route path="/about" element={<About />} />
  ```

:six: **Important** : Dans React Router v6, l'élément du composant est passé via la prop `element`, et non plus `component` ou `render` comme c’était le cas dans les versions précédentes.

#### Navigation avec `Link` et `NavLink`

- **`Link`** : Le composant `Link` remplace les balises `<a>` pour permettre la navigation sans recharger la page.

  ```tsx
  import { Link } from 'react-router-dom';

  const NavBar: React.FC = () => {
    return (
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </nav>
    );
  };
  ```

- **`NavLink`** : Fonctionne de la même manière que `Link`, mais il permet d’ajouter des styles conditionnels en fonction de l’URL active.

  ```tsx
  import { NavLink } from 'react-router-dom';

  const NavBar: React.FC = () => {
    return (
      <nav>
        <NavLink to="/" style={({ isActive }) => isActive ? { fontWeight: 'bold' } : {}}>Home</NavLink>
        <NavLink to="/about" style={({ isActive }) => isActive ? { fontWeight: 'bold' } : {}}>About</NavLink>
      </nav>
    );
  };
  ```

#### Paramètres dans l'URL

Avec React Router v6, tu peux accéder aux paramètres d'URL à l'aide de `useParams`.

- **Exemple de route avec paramètres dynamiques** :

  ```tsx
  import { useParams } from 'react-router-dom';

  const Product: React.FC = () => {
    const { id } = useParams<{ id: string }>();
    return <div>Product ID: {id}</div>;
  };

  const App: React.FC = () => {
    return (
      <BrowserRouter>
        <Routes>
          <Route path="/product/:id" element={<Product />} />
        </Routes>
      </BrowserRouter>
    );
  };
  ```

#### Routes imbriquées

React Router v6 facilite la définition de **routes imbriquées**.

- **Exemple** :

  ```tsx
  const Dashboard: React.FC = () => {
    return (
      <div>
        <h2>Dashboard</h2>
        <Routes>
          <Route path="profile" element={<Profile />} />
          <Route path="settings" element={<Settings />} />
        </Routes>
      </div>
    );
  };

  const App: React.FC = () => {
    return (
      <BrowserRouter>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="dashboard/*" element={<Dashboard />} />
        </Routes>
      </BrowserRouter>
    );
  };
  ```

Dans cet exemple, l'URL `/dashboard/profile` et `/dashboard/settings` affichera respectivement les composants **`Profile`** et **`Settings`** dans la section du **`Dashboard`**.

### Redirection et Navigation Programmatique

#### `Navigate` pour la redirection

Si tu veux rediriger un utilisateur après une action, tu peux utiliser le composant **`Navigate`**.

- Exemple de redirection après un clic :

  ```tsx
  import { Navigate } from 'react-router-dom';

  const RedirectExample: React.FC = () => {
    const redirectToAbout = true;  // Simuler une condition
    return redirectToAbout ? <Navigate to="/about" /> : <Home />;
  };
  ```

:six: **Conseil** : Utilise **`Navigate`** pour effectuer des redirections conditionnelles de manière déclarative.

#### `useNavigate` pour la navigation programmatique

Tu peux aussi utiliser le hook **`useNavigate`** pour effectuer des redirections dans des événements, comme une soumission de formulaire ou un autre comportement utilisateur.

- Exemple de navigation programmatique :

  ```tsx
  import { useNavigate } from 'react-router-dom';

  const FormSubmit: React.FC = () => {
    const navigate = useNavigate();

    const handleSubmit = () => {
      // Logique de soumission
      navigate('/thank-you');
    };

    return <button onClick={handleSubmit}>Submit</button>;
  };
  ```

:six: **Conseil** : Utilise `useNavigate` pour naviguer programmétiquement en réponse à des actions de l'utilisateur, comme la soumission de formulaire ou après des traitements asynchrones.

### Authentification et Protection des Routes

#### Protéger les routes avec un composant de garde

Tu peux facilement protéger des routes en utilisant une logique conditionnelle dans ton composant **`Route`**.

- Exemple avec un composant de garde d'authentification :

  ```tsx
  import { Navigate, Route, Routes } from 'react-router-dom';

  const ProtectedRoute: React.FC<{ element: JSX.Element; isAuthenticated: boolean }> = ({ element, isAuthenticated }) => {
    return isAuthenticated ? element : <Navigate to="/login" />;
  };

  const App: React.FC = () => {
    const isAuthenticated = true; // Exemple simple, tu peux récupérer cette info via un Context API

    return (
      <BrowserRouter>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/login" element={<Login />} />
          <Route path="/dashboard" element={<ProtectedRoute element={<Dashboard />} isAuthenticated={isAuthenticated} />} />
        </Routes>
      </BrowserRouter>
    );
  };
  ```

:six: **Conseil** : Utilise des routes protégées pour sécuriser des pages qui nécessitent une authentification, comme des dashboards ou des pages privées.

---

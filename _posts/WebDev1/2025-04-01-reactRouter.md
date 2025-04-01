---
layout: post
title: Introduction à React Router avec TypeScript
categories:
- WebDev1
- lecture
author: Yoann Pigné
published: true
---


## Présentation de React Router

React Router est une bibliothèque permettant de gérer la navigation dans une application React de manière déclarative. Elle facilite la création de **routes dynamiques** et permet de naviguer entre les pages sans recharger l'application.

### Avantages de React Router
- **Navigation fluide** : Changement de pages sans rechargement du navigateur.
- **Routes dynamiques** : Possibilité de créer des chemins paramétrés.
- **Gestion des redirections** : Redirections conditionnelles et pages d'erreur 404.
- **Historique et URL Management** : Gestion simplifiée de l'historique de navigation.

---

## Les trois modes de React Router

L'API est organisée en trois modes principaux :

### 1) Framework Mode
React Router agit comme un framework en offrant une gestion avancée de la navigation, du chargement de données et des actions.

- Utilisation des loaders et actions avec les `Route Objects`
- Gestion des mutations de données directement dans les routes
- Excellente intégration avec des solutions comme Remix

Exemple :
```tsx
const router = createBrowserRouter([
  {
    path: "/",
    element: <Home />, 
    loader: () => fetch("/api/home"),
  },
]);
```

### 2) Data Mode
Ce mode met l'accent sur la **gestion des données** en associant chaque route à des chargements de données (loaders) et des actions.

- Idéal pour précharger les données avant l'affichage d'un composant
- Utilisation des `loaders` et `actions` pour simplifier les requêtes API

Exemple :
```tsx
export const loader = async () => {
  const response = await fetch("/api/data");
  return response.json();
};
```
Puis dans le composant :
```tsx
const data = useLoaderData();
```

### 3) Declarative Mode
C'est le mode classique de React Router, où les routes sont déclarées avec JSX et gérées avec des composants comme `Routes` et `Route`.

Exemple avec routes imbriquées et routage dynamique :
```tsx
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/about" element={<About />} />
  <Route path="/users" element={<Users />} />
</Routes>
```

---

## Installation de React Router

Pour utiliser React Router avec TypeScript, installez la bibliothèque avec npm :

```bash
npm install react-router-dom
```

Si vous utilisez TypeScript, installez également les types :

```bash
npm install @types/react-router-dom
```

---

## Définition des Routes

L'application React doit être enveloppée dans un `BrowserRouter` pour activer la navigation.

### Exemple : Définir des routes de base

```tsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
import Home from "./pages/Home";
import About from "./pages/About";

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

export default App;
```

---

## Navigation avec `Link` et `useNavigate`

### Utilisation de `Link`

Le composant `Link` permet de créer des liens internes sans rechargement de page :

```tsx
import { Link } from "react-router-dom";

const Navbar: React.FC = () => {
  return (
    <nav>
      <Link to="/">Accueil</Link>
      <Link to="/about">A propos</Link>
    </nav>
  );
};
```

### Utilisation de `useNavigate`

Le hook `useNavigate` permet de naviguer de manière programmatique :

```tsx
import { useNavigate } from "react-router-dom";

const Home: React.FC = () => {
  const navigate = useNavigate();

  return (
    <div>
      <h1>Accueil</h1>
      <button onClick={() => navigate("/about")}>Aller à propos</button>
    </div>
  );
};
```

---

## Routes Paramétriques et Imbriquées

Les routes paramétriques permettent de récupérer des valeurs dynamiques via l'URL.

### Exemple : Gestion des routes dynamiques

```tsx
<Routes>
    <Route path="/user/:userId" element={<UserDetail />} />
</Routes>
```

Dans `Users.tsx`, affichons les liens vers des utilisateurs dynamiques :
```tsx
<Link to="/user/1">Utilisateur 1</Link>
<Link to="/user/2">Utilisateur 2</Link>
```

Dans `UserDetail.tsx` :
```tsx
import { useParams } from "react-router-dom";

const UserDetail: React.FC = () => {
  const { userId } = useParams();
  return <h2>Profil de l'utilisateur {userId}</h2>;
};
```

---

## Imbrication des Routes et `Outlet`

Le composant `Outlet` est utilisé pour rendre les sous-composants d'une route imbriquée.

### Exemple : Routes imbriquées
```tsx
<Routes>
  <Route path="/dashboard" element={<Dashboard />}>
    <Route path="stats" element={<Stats />} />
    <Route path="settings" element={<Settings />} />
  </Route>
</Routes>
```

Dans `Dashboard.tsx` :
```tsx
import { Outlet } from "react-router-dom";

const Dashboard: React.FC = () => {
  return (
    <div>
      <h1>Dashboard</h1>
      <Outlet />
    </div>
  );
};
```



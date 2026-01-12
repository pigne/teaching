---
layout: post
title: Graphiques et visualisation de données
categories:
- WebDev2
- lecture
author: Yoann Pigné
published: true
---

<!--toc:start-->
- [Le pipeline de la visualisation](#le-pipeline-de-la-visualisation)
- [Les modèles de rendu du Web](#les-modèles-de-rendu-du-web)
- [Dessin 2D avec Canvas](#dessin-2d-avec-canvas)
- [Rendu GPU et 3D avec WebGL](#rendu-gpu-et-3d-avec-webgl)
- [SVG — Graphismes vectoriels dans le DOM](#svg-graphismes-vectoriels-dans-le-dom)
- [D3.js — Data-Driven Documents](#d3js-data-driven-documents)
- [La jointure de données](#la-jointure-de-données)
- [Visualisation déclarative avec Vega](#visualisation-déclarative-avec-vega)
- [Performances et passage à l’échelle](#performances-et-passage-à-léchelle)
- [D3 et React](#d3-et-react)
- [Stack moderne de visualisation](#stack-moderne-de-visualisation)
- [Exemple react +  d3 + svg](#exemple-react-d3-svg)
<!--toc:end-->

Un des objectifs fondamentaux du Web est de **présenter et échanger de l’information**.
Cette information peut être textuelle, visuelle, spatiale, temporelle ou interactive.

Les représentations graphiques peuvent être :

* **statiques** (images, graphiques pré-rendus)
* **dynamiques** (générées à partir de données)

Une visualisation dynamique transforme des **données** en **structures graphiques** qui peuvent être explorées, filtrées et manipulées.

Ces graphiques peuvent être générés :

* côté **serveur** (images, PDF, tuiles),
* côté **client** grâce au navigateur, au GPU et à JavaScript.

---

## Le pipeline de la visualisation

Toutes les bibliothèques modernes de visualisation suivent le même schéma conceptuel :

```
Données → Transformation → Mise en page → Encodage → Rendu → Interaction
```

| Étape          | Rôle                         |
| -------------- | ---------------------------- |
| Données        | CSV, JSON, API, bases        |
| Transformation | filtrer, agréger, normaliser |
| Mise en page   | calculer les positions       |
| Encodage       | couleur, taille, forme       |
| Rendu          | SVG, Canvas, WebGL           |
| Interaction    | zoom, survol, sélection      |

D3, Vega, Deck.gl, Tableau, PowerBI implémentent tous ce pipeline.

---

## Les modèles de rendu du Web

Le navigateur propose **trois modèles graphiques** :

| Modèle        | Technologie | Principe                     |
| ------------- | ----------- | ---------------------------- |
| Vectoriel DOM | SVG         | chaque forme est un nœud DOM |
| Bitmap        | Canvas      | un buffer de pixels          |
| GPU           | WebGL       | rendu via shaders            |

Chaque modèle implique :

* des performances différentes,
* une gestion différente des événements,
* une architecture différente.

---

## Dessin 2D avec Canvas

`<canvas>` fournit un **buffer de pixels**.

Une fois qu’un objet est dessiné, il n’existe plus que sous forme de pixels.

```js
var canvas = document.getElementById("c");
var ctx = canvas.getContext("2d");

ctx.fillStyle = "red";
ctx.fillRect(10, 10, 100, 100);
```

Canvas est :

* rapide,
* simple,
* non structuré,
* non accessible au DOM.

Utilisé pour :

* jeux,
* particules,
* grandes quantités d’objets.

Librairies :

* p5.js
* PixiJS (moteur 2D accéléré par WebGL)

---

## Rendu GPU et 3D avec WebGL

WebGL permet d’utiliser la carte graphique via JavaScript.

C’est :

* extrêmement rapide,
* basé sur des shaders,
* bas niveau.

On utilise rarement WebGL directement. On passe par :

| Librairie  | Usage                  |
| ---------- | ---------------------- |
| Three.js   | scènes 3D              |
| Deck.gl    | grandes visualisations |
| Babylon.js | moteurs 3D             |
| Regl       | WebGL bas niveau       |

WebGL devient indispensable pour :

* cartes,
* nuages de points,
* big data graphique.

---

## SVG — Graphismes vectoriels dans le DOM

SVG est un langage XML intégré au DOM.

Chaque forme est un élément DOM :

```html
<circle cx="50" cy="50" r="20" fill="red"/>
```

SVG offre :

* événements,
* CSS,
* transformations,
* animations.

SVG est idéal pour :

* petits jeux de données,
* interfaces,
* diagrammes.

Il devient lent au-delà de quelques milliers d’éléments.

---

## D3.js — Data-Driven Documents

[D3](https://d3js.org/) relie des **données** au **DOM**.

Il ne dessine pas directement :
il calcule, projette et synchronise.

D3 fournit :

* échelles,
* layouts,
* géométrie,
* jointures de données.

Le rendu est fait par :

* SVG,
* Canvas,
* WebGL.

Usage moderne :

> D3 calcule, un autre moteur dessine.

---

## La jointure de données

```js
svg.selectAll("circle")
  .data(data)
  .join("circle")
  .attr("cx", d => x(d.x))
  .attr("cy", d => y(d.y))
  .attr("r", 5);
```

Cela synchronise le DOM avec les données.

---

## Visualisation déclarative avec Vega

[Vega](https://vega.github.io/vega/) décrit des graphiques via JSON.

```json
{
  "mark": "bar",
  "encoding": {
    "x": {"field": "année"},
    "y": {"aggregate": "sum", "field": "ventes"}
  }
}
```

| D3            | Vega           |
| ------------- | -------------- |
| impératif     | déclaratif     |
| code          | description    |
| très flexible | plus contraint |

---

## Performances et passage à l’échelle

| Nombre d’éléments | Technologie |
| ----------------- | ----------- |
| < 1 000           | SVG         |
| 1 000 – 100 000   | Canvas      |
| > 100 000         | WebGL       |

Le DOM est coûteux.
Les pixels sont rapides.
Le GPU est imbattable.

---

## D3 et React

React gère l’**état**.
D3 gère les **calculs**.

Architecture typique :

```
État React → D3 (scales, layout) → SVG ou Canvas
```

Ne jamais laisser React et D3 modifier les mêmes nœuds DOM.

Librairies modernes :

* Nivo
* Recharts
* Visx
* ECharts
* Observable Plot

---

## Stack moderne de visualisation

En 2025, une application typique :

| Couche         | Outils                    |
| -------------- | ------------------------- |
| Données        | APIs, Arrow, DuckDB       |
| Transformation | D3, Arquero               |
| Mise en page   | D3, Vega                  |
| Rendu          | SVG, Canvas, WebGL        |
| UI             | React                     |
| Haut niveau    | Vega, Deck.gl, Observable |




## Exemple react +  d3 + svg


* React → état
* D3 → calcul (scales, layout, géométrie)
* **SVG → rendu**

Sans jamais laisser D3 toucher au DOM.

```jsx
import { useMemo, useState } from "react";
import * as d3 from "d3";

/**
 * === 0. RAW DATA =====================================
 * Données brutes, telles qu’elles arrivent depuis l’API.
 * Aucune sémantique graphique ici.
 */
const data = [
  { region: "IDF", value: 120, volume: 3000 },
  { region: "IDF", value: 80,  volume: 2000 },
  { region: "NAQ", value: 150, volume: 5000 },
  { region: "PAC", value: 60,  volume: 1000 },
  { region: "NAQ", value: 110, volume: 4200 }
];

export default function ScatterPlot() {

  /**
   * === 1. INTERACTION / STATE ==========================
   * L’utilisateur agit sur l’interface → cela modifie l’état React.
   * Cet état est l’entrée du pipeline.
   */
  const [region, setRegion] = useState(null);

  const width = 500;
  const height = 300;

  /**
   * === 2. DATA TRANSFORM ==============================
   * Sélection, filtrage, agrégation.
   * On produit une table de données "logiques"
   * prête à être encodée graphiquement.
   */
  const filteredData = useMemo(() => {
    return region
      ? data.filter(d => d.region === region)
      : data;
  }, [region]);

  /**
   * === 3. LAYOUT (D3) =================================
   * On calcule comment les données vont être projetées
   * dans l’espace graphique (échelles).
   * D3 joue ici le rôle de moteur mathématique.
   */
  const xScale = useMemo(() => {
    return d3.scaleLinear()
      .domain(d3.extent(filteredData, d => d.value))
      .range([40, width - 20]);
  }, [filteredData]);

  const yScale = useMemo(() => {
    return d3.scaleLinear()
      .domain(d3.extent(filteredData, d => d.volume))
      .range([height - 30, 20]);
  }, [filteredData]);

  const colorScale = d3.scaleOrdinal()
    .domain(["IDF", "NAQ", "PAC"])
    .range(["red", "blue", "green"]);

  /**
   * === 4. ENCODING ===================================
   * On transforme chaque ligne de données
   * en primitives graphiques abstraites.
   * (positions, tailles, couleurs)
   */
  const visualMarks = filteredData.map(d => ({
    cx: xScale(d.value),
    cy: yScale(d.volume),
    r: 6,
    fill: colorScale(d.region)
  }));

  /**
   * === 5. RENDERING ==================================
   * React + SVG produisent l’image finale.
   * D3 ne touche jamais le DOM.
   */
  return (
    <div>

      {/* === INTERACTION ============================== */}
      <select onChange={e => setRegion(e.target.value || null)}>
        <option value="">Toutes régions</option>
        <option value="IDF">IDF</option>
        <option value="NAQ">NAQ</option>
        <option value="PAC">PAC</option>
      </select>

      <svg width={width} height={height}>

        {/* Axes (rendu pur SVG) */}
        <line x1="40" y1="20" x2="40" y2={height - 30} stroke="#999" />
        <line x1="40" y1={height - 30} x2={width - 20} y2={height - 30} stroke="#999" />

        {/* Marques visuelles (résultat du pipeline) */}
        {visualMarks.map((m, i) => (
          <circle
            key={i}
            cx={m.cx}
            cy={m.cy}
            r={m.r}
            fill={m.fill}
          />
        ))}

      </svg>
    </div>
  );
}
```

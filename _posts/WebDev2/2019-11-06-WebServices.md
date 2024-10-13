---
layout: post
title: Web APIs / Web Services
categories:
- WebDev2
- lecture
author: Yoann Pigné
published: true
update: 2024-10-13
---

# Sommaire

- [Sommaire](#sommaire)
  - [Introduction](#introduction)
  - [REST vs SOAP](#rest-vs-soap)
    - [SOAP (Simple Object Access Protocol)](#soap-simple-object-access-protocol)
    - [REST](#rest)
    - [Avantages et inconvénients](#avantages-et-inconvénients)
    - [Complément sur les performances](#complément-sur-les-performances)
  - [GraphQL](#graphql)
    - [Limites de GraphQL](#limites-de-graphql)
  - [Les outils Swagger](#les-outils-swagger)
    - [Conception d'une API avec Swagger](#conception-dune-api-avec-swagger)
    - [Spécification des paramètres](#spécification-des-paramètres)
    - [Génération de code et tests](#génération-de-code-et-tests)

## Introduction

Il existe plusieurs technologies pour créer des services web, chacune étant adaptée à un type de service spécifique que l'on souhaite fournir au client.

## REST vs SOAP

Deux technologies sont souvent comparées :

- **REST**
- **SOAP**

### SOAP (Simple Object Access Protocol)

SOAP est une technologie **non limitée au web** (HTTP, SMTP, UDP, TCP, etc.) qui facilite la manipulation de **services**. Contrairement à REST, SOAP ne donne pas directement accès à des données mais à des services/actions dans une application serveur exposée.

SOAP utilise un langage XML standardisé (WSDL) pour décrire les services disponibles et les actions à exécuter.

**Exemples de méthodes SOAP :**

```java
switchProtocol(Protocol)
upgradeAccountToPremium(User)
resendAuthenticationToken(User, Token)
```

**Exemple de requête SOAP :**

```xml
POST /InStock HTTP/1.1
Host: www.example.org
Content-Type: application/soap+xml; charset=utf-8
Content-Length: nnn

<?xml version="1.0"?>
<soap:Envelope
  xmlns:soap="http://www.w3.org/2003/05/soap-envelope/"
  soap:encodingStyle="http://www.w3.org/2003/05/soap-encoding">
  <soap:Body xmlns:m="http://www.example.org/stock">
    <m:GetStockPrice>
      <m:StockName>IBM</m:StockName>
    </m:GetStockPrice>
  </soap:Body>
</soap:Envelope>
```

SOAP est extensible, ce qui permet d'ajouter des fonctionnalités telles que la sécurité (WS-Security), la gestion des transactions (WS-AtomicTransaction), et plus encore. C'est pourquoi SOAP est souvent choisi dans des environnements où la sécurité et la fiabilité des transactions sont primordiales (par exemple, dans les secteurs bancaires ou d'assurance).

### REST

REST est une technologie **orientée web** qui expose une interface publique (API) pour l'accès et la manipulation (*Create, Read, Update, Delete* - CRUD) de **données**.

REST fonctionne exclusivement sur HTTP(s) et tire parti de ses mécanismes (URI, verbes, codes de retour), mais n'impose pas de format de données spécifique (JSON, XML, etc.).

**Exemples de méthodes REST :**

```java
getUserInformation(UserId)
addProductToBasket(ProductId, BasketId)
```

Les requêtes REST sont **sans état** (*stateless*), ce qui signifie que chaque requête est indépendante des autres. Cela permet de mettre en place des mécanismes de **cache**, ce qui améliore les performances et la rapidité des réponses pour les ressources fréquemment consultées. REST est souvent préféré pour sa courbe d'apprentissage plus douce et sa flexibilité.

Un outil comme [Swagger](http://swagger.io/) peut être utilisé pour :

- définir des API REST ;
- générer automatiquement le code des serveurs et des clients pour ces API.

### Avantages et inconvénients

| Critère | SOAP | REST |
|---------|------|------|
| **Extensibilité** | Très extensible grâce à des protocoles comme WS-Security, WS-AtomicTransaction, etc. | Moins extensible, mais très simple à utiliser. |
| **Complexité des requêtes** | Permet des requêtes complexes (jointures, filtres, etc.). | Accès simple aux données ; requêtes complexes nécessitent plusieurs appels. |
| **Stateless** | Non (garde un état entre les requêtes). | Oui (chaque requête est indépendante). |
| **Mise en cache** | Pas de cache possible. | Possibilité de mise en cache grâce au caractère *stateless*. |
| **Facilité de mise en œuvre** | Plus complexe à mettre en place. | Plus simple à mettre en œuvre et à maintenir. |
| **Utilisation typique** | Souvent utilisé dans des environnements à forte exigence de sécurité et de fiabilité (ex. banques). | Populaire pour les API web grand public et les applications mobiles. |

### Complément sur les performances

- REST peut avoir l'avantage en termes de performances sur le web, car il exploite des méthodes HTTP comme *GET* qui peuvent être mises en cache par les navigateurs ou les serveurs.
- SOAP, en revanche, est plus robuste pour des interactions complexes entre services, mais son format XML et ses fonctionnalités avancées peuvent introduire une certaine lourdeur dans la communication.

## GraphQL

[GraphQL](http://graphql.org/), développé par *Facebook*, propose une approche similaire à REST (API de manipulation de données) tout en offrant certains des avantages de SOAP (requêtes complexes).

- **Dédié à la manipulation de données** uniquement (comme REST).
- Les requêtes sont exprimées dans un langage dédié (*GraphQL*), similaire au WSDL de SOAP.
- Les réponses sont structurées en objets JSON.
- **Flexibilité pour le client** : GraphQL permet aux clients de spécifier exactement les champs de données souhaités, réduisant ainsi la surcharge réseau et la quantité de données transférées.

### Limites de GraphQL

- Bien que plus flexible, GraphQL peut entraîner une surcharge côté serveur si les requêtes sont très complexes et mal optimisées.
- Sa courbe d'apprentissage peut être plus raide que celle de REST pour les développeurs débutants, et sa mise en œuvre peut être plus complexe pour garantir la performance et la sécurité.

## Les outils Swagger

Swagger propose plusieurs outils pour aider à créer des API cohérentes et maintenables.

Il permet de concevoir des API selon une nomenclature standardisée. Swagger est à l'origine de la spécification [OpenAPI](https://swagger.io/specification/), devenue un standard pour la documentation des API REST, facilitant l'interopérabilité entre différents services.

### Conception d'une API avec Swagger

Pour concevoir une API, Swagger propose un éditeur qui valide les définitions. Il peut être utilisé [en ligne](https://editor.swagger.io/) ou installé localement à partir des [sources](https://github.com/swagger-api/swagger-editor) ou via Docker :

```bash
docker pull swaggerapi/swagger-editor
docker run -d -p 80:8080 swaggerapi/swagger-editor
```

### Spécification des paramètres

Il existe trois façons de spécifier des paramètres ou d'envoyer des données dans Swagger :

1. **Dans le corps d'une requête POST** :
   ```yaml
   requestBody:
     description: Created user object
     content:
       application/json:
         schema:
           $ref: '#/components/schemas/User'
   ```

2. **En paramètre d'une URI dans une requête GET** :
   ```yaml
   parameters:
     - name: monParametre
       in: query
   ```

3. **Dans l'URL** :
   ```yaml
   /url/url/{monParametre}:
     get:
       ...
       parameters:
         - name: monParametre
           in: path
   ```

### Génération de code et tests

Swagger permet de générer automatiquement des serveurs et des clients à partir de la spécification OpenAPI.

On peut tester une API simplement avec la commande `curl` :

```bash
curl -X GET --header 'Accept: application/json' 'https://api.example.com/1.0.0/ping'
```

Il est aussi possible de tester avec un script JavaScript qui utilise le client généré automatiquement par Swagger.

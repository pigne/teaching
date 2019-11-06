---
layout: post
title: Web APIs / Web Services
categories:
- WebDev2
- lecture
author: Yoann Pigné
published: true
---

- [REST vs SOAP](#rest-vs-soap)
- [GraphQL](#graphql)
- [Les outils SWAGGER](#les-outils-swagger)

Plusieurs technologies pour faire des services web en fonction du type de service que l'on veut fournir au client.


## REST vs SOAP

Souvent 2 technologies s'oppose:

- REST
- SOAP

### SOAP

SOAP est une technologie **pas seulement Web** (HTTP, SMTP, UDP, TCP, ...) dédiée à la mise en place d'outils de manipulation de **services**. SOAP ne donne pas accès a des données, il donne accès à des services/actions dans une application serveur exposée.

SOAP utilise un langage XML normalisé (WSDL) pour décrire les services à utiliser et les actions à exécuter.

```java
switchProtocol(Protocol)
upgradeAccountToPremium(User)
resendAuthentificationToken(User, Token)
```

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


L'extensibilité offre de nouvelles fonctionnalités (sécurité, atomicité, etc..)

### REST

REST est une technologie **Web** dédiée à la mise à disposition d'une interface publique (API) pour l'accès et la manipulation (CRUD) de **données**.

REST ne fonctionne qu'avec HTTP(s) et utilise ses mécanismes (URI) mais ne limite pas les formats de données.

```java
getUserInformation(UserId)
addProductToBasket(ProductId, BasketId)
```

Les requêtes REST sont sans état (*stateless*). On peut décrire une API REST avec les mécanismes de HTTP (méthodes, URI, codes de retour).

Par exemple on peut utiliser un outil comme [Swagger](http://swagger.io/) pour :
- définir des API REST
- auto-générer les codes de serveurs et de clients pour ces API.

### Pros / Cons

- A priori SOAP est plus complet et général que REST, surtout du fait de son extensibilité.
- SOAP donne beaucoup de pouvoir au client (concevoir des requêtes complexes avec jointures, filtres, etc.).
- REST ne permet qu'un accès simple aux données. Une requête complexe (avec jointures, filtres, etc.) nécessitera plusieurs appel REST.
- REST est sans état (*stateless*) et permet la mise en place de mécanismes de **cache**. SOAP n'est pas *stateless*, donc **pas de cache possible**.
- il semble que REST est plus facile a mettre en place et à maintenir que SOAP.

## GraphQL

*Facebook* propose [*GraphQL*](http://graphql.org/), une approche proche de REST avec les avantages (requêtes complexes) de SOAP. *GraphQL* est un langage de description qui permet au client de décrire le type de réponse qu'il désire recevoir du server.

- Dédié à la manipulation de données uniquement (comme REST)
- Requête dans un langage dédié (*GraphQL*) (à l'image des requêtes WSDL de SOAP) pour décrire la requête.
- Réponses sous forme d'objets JSON.

## Les outils SWAGGER

Swagger propose plusieurs outils pour nous aider a créer des API cohérentes et maintenables. 

D'abord on nous propose de concevoir des APIs avec une nomenclature fixée. Swagger est à l'origine de la spécification [OpenAPI](https://swagger.io/specification/).

Pour concevoir une API Swagger nous propose un éditeur qui valide la saisie. Il peut être utilisé [en ligne](https://editor.swagger.io/) ou être téléchargé et exécuté en local à partir les [sources](https://github.com/swagger-api/swagger-editor) ou via docker:

```bash
docker pull swaggerapi/swagger-editor
docker run -d -p 80:8080 swaggerapi/swagger-editor
```

Attention il y a 3 façons de spécifier des paramètre ou d'envoyer des données :

- dans le corps d'une requête en méthode POST :
  ```yaml
  in: "body"
  ```
- en paramètre d'une URI en méthode GET :
  ```yaml
  in: "query"
  ```
- dans l'URI :
  ```yaml
  in: "path"
  ```


  Ensuite on peu générer des serveurs et des clients automatiquement à partir de cette spécification. 

  
  On teste simplement une API avec la commande `curl` : 

```sh
curl -X GET --header 'Accept: application/json' 'https://api.example.com/1.0.0/ping'
```

On peut aussi tester avec un script JS qui utilise le client généré automatiquement par Swagger. 

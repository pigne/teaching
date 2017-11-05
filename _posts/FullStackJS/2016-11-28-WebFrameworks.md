---
layout: post
title: Web Frameworks
categories:
- FullStackJS
- lecture
author: Yoann Pigné
published: false
---


Web development is getting dominated by the MVC (Model View Controller) design pattern. Both on the server and client side.

Web development relies on well established protocols and standards.

- HTTP
- REST

Common problems need to be addressed

- Security
- Browsers' compatibility

Tools and frameworks help in every steps

- MVC Frameworks (Client/Server-Side)
- Testing Frameworks
- Continuous integration platforms
- Task Runners
- Scaffolding
- ...

We focus here on node.js and the javascript ecosystem.


## Hypertext Transfer Protocol (HTTP)

- Application-level protocol for distributed systems. Generic and stateless.
- Request message (from client to server)
- Response message (from server to client)
- Text-based messages / Multipurpose Internet Mail Extensions (MIME) format
- Messages :

```
generic-message = start-line
                *(message-header CRLF)
                CRLF
                [ message-body ]
start-line      = Request-Line | Status-Line
```


### HTTP Headers and Entities

```
message-header = field-name ":" [ field-value ]
field-name     = token
field-value    = *( field-content | LWS )
field-content  = <the OCTETs making up the field-value
                 and consisting of either *TEXT or combinations
                 of token, separators, and quoted-string>
```

Example header:

```
Host: tools.ietf.org
Connection: keep-alive
Cache-Control: max-age=0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Encoding: gzip,deflate,sdch
Accept-Language: en-US,en;q=0.8
Accept-Charset: UTF-8,*;q=0.5
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_2) AppleWebKit/537.17 (KHTML, like Gecko) Chrome/24.0.1312.57 Safari/537.17
```


### HTTP Requests

From the client to the server
```
Request = Request-Line
          *(( general-header
            | request-header
            | entity-header ) CRLF)
          CRLF
          [ message-body ]
```

Request Line

```
Request-Line = Method SP Request-URI SP HTTP-Version CRLF
```

### HTTP Methods

- "OPTIONS": information about the communication options available
- "GET": retrieve whatever information is identified by the Request-URI
- "HEAD": same as "GET" message-body in the response
- "POST": append entity to the existing Request-URI
- "PUT": store entity as the new Request-URI
- "DELETE": delete existing Request-URI
- "TRACE" : see what is being received at the other end of the request chain
- "CONNECT" : for use with a proxy that can dynamically switch to being a tunnel (e.g. SSL tunneling).


### HTTP Responses

```
Response    = Status-Line
              *(( general-header
               | response-header
               | entity-header ) CRLF)
              CRLF
              [ message-body ]
Status-Line = HTTP-Version SP Status-Code SP Reason-Phrase CRLF
```

Status Codes:

- **1xx**: Informational - Request received, continuing process
- **2xx**: Success - The action was received, understood, and accepted
- **3xx**: Redirection - Further action must be taken to complete the request
- **4xx**: Client Error - The request contains bad syntax or cannot be fulfilled
- **5xx**: Server Error - The server failed to fulfill an apparently valid request



## Representational State Transfer

- REST is a style of software architecture for distributed systems on top of HTTP.
- Each resource is accessed through one unique request (URI).
- Requests are stateless (identification within the URI)
- Resources are accessed one by one or as collections
- RESTful web service (RESTful web API)




### RESTful Web Services

- RESTful API and HTTP methods

| Resource    | GET | PUT| POST | DELETE |
|-------------|-----|----|------|--------|
| Collection URI | List elements | Replace entire collection | Create new element in collection  | Delete collection |
| Element URI | Retrieve one element | Replace existing element | Generally not used | Delete one element |

- Plural may indicate a collection e.g. `http://example.com/emails/`
- An id may indicate an element e.g. `http://example.com/email/17/`
- Can be prefixed with an API version e.g. `https://api.twitter.com/1.1/statuses/home_timeline.json` or a keyword (`api`) in order to distinguish from normal web pages.
home_timeline.json




## Security

Too many vulnerabilities exist... But developers *are* responsible for their code!

Common Attacks

- Cross-Site Request Forgery (CSRF)
- Cross-site scripting (XSS)
- SQL injection

Common Measures

- Anti-CSRF tokens
- Forms Timeouts
- Escape users inputs

**Reduce vulnerability... Use frameworks!**



## The Model–View–Controller Design Pattern

- Improve the separation of concerns
- Facilitate automated unit testing
- Facilitate team work
- **MODEL**
  * Holds the data
  * Links to persistent storage (DBMS)
  * Ignores other components
- **VIEW**
   * Representation of data
   * What users see
   * May know the **Model**
- **CONTROLLER**
   * Handles users requests
   * Updates **Model** data
   * Triggers **Views**




### Classical Server-Side MVC Schema

  ![Server-Side MVC Schema]({{ site.baseurl }}/images/serverSideMVC.svg)

The 3 MVC parts run on the server side.


### Server-Side MVC Sequence

![Server-Side MVC Sequence]({{ site.baseurl }}/images/sequenceServerMVC.svg)

### Client-Side MVC Schema

![Client-Side MVC Schema]({{ site.baseurl }}/images/clientSideMVC.svg)

The 3 MVC parts run on the client side. The server is just a gateway for the DBMS.

### Client-Side MVC Sequence

![Client MVC Sequence]({{ site.baseurl }}/images/sequenceClientMVC.svg)


## Tools for the JS ecosystem

- JS Interpreter:
  * `node` ([nodejs.org](http://nodejs.org/))
- Dependencies Management:
  * client side:
     - :negative_squared_cross_mark: `bower` ([bower.io](http://bower.io/))
     - :negative_squared_cross_mark: `duo` ([duojs.org](http://duojs.org/))
  * server side:
      - :white_check_mark: `npm` (packaged with node)
      - :white_check_mark: `yarn` (Octobre 2016 - peut être le remplaçant de npm?)
- Task Runner:
  * `grunt` ([gruntjs.com](http://gruntjs.com))
  * `gulp`, `brunch`, ...
- Module Builder for Browsers
  * [`webpack`](https://webpack.github.io/docs/)
  * `browserify`
- Projects Scaffolding:
  * `yeoman` ([yeoman.io](http://yeoman.io/))

## MVC Frameworks

### Server Side MVC Frameworks

many frameworks in many (all?) languages

#### Java
- Spring MVC
- Struts 2
- Stripes
- Wicket (Apache)
- GWT (Google)
- And off course the JAVA EE ecosystem...

#### PHP
- Zend Framework (IBM)
- Symphony
- CakePHP (Open Source)
- ...

#### Python
- Django
- web2py
- Zope

#### Ruby
- Ruby on Rails

#### JavaScript (node.js)
- Express



### Client Side MVC Frameworks

Obviously in JavaScript... actually not only

- Angular.js
- Ember.js
- Backbone.js
- Cappuccino (in its own language Objective-J, emulates Apple's Cocoa style)
- Knockout.js (MVVM design pattern)
- Javascript MVC
- Batman.js
- GWT
- Vue.js
- The React ecosystem...

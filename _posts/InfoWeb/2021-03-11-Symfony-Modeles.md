---
layout: post
title: Gestion des modèles de données avec Symfony
categories:
- InfoWeb
- lecture
author: Yoann Pigné
published: true
update: 2022-02-21
---

Récapitulatif du cours et du TP précédent :

- Création d'une route (au sens de HTTP/REST)
- Connexion de cette route à un contrôleur (on utilise les annotations ou le langage YAML)
- Création et envoie d'un objet `Response` dans le contrôleur
- Délégation de la génération de la réponse (en HTML) à une vue utilisant le langage `TWIG`
- Utilisation du mécanisme de gestion des **sessions** de Symfony

Au programme aujourd'hui la **gestion des modèles objets et la persistance des données**:

- Configuration de la base de données
- Représentation d'un modèle objet avec `Doctrine`. Notion d'ORM (_Object-Relational Mapping_).
- Conception d'un modèle objet
- Connexion du modèle objet à la base de données
- **Création** / **Lecture** / **Modification** / **Suppression** de données persistantes
- Utilisation des `Repository`
- Jointures entre modèles

Ce que l'on verra plus tard:

- Génération de formulaires à partir de modèles objets
- Validation de formulaires
- Gestion des utilisateurs et des droits dans une application Symfony


## Installation et configuration

Symfony seul ne permet pas de gérer des modèles objets ni de se connecter à une base de données. On utilise **Doctrine**, un ORM pour mettre en concordance des objets PHP avec un modèle persistant (Base de données).

Doctrine doit être installé avec `composer` : 

```bash
composer require symfony/orm-pack
composer require --dev symfony/maker-bundle
```

Les informations de connexion à la base de donnée sont stockés dans la variable d'environnement `DATABASE_URL`. En mode développement on peut renseigner cette variable dans les fichier `.env` à la racine du projet. 

```bash
# to use mariadb:
DATABASE_URL="mysql://db_user:db_password@127.0.0.1:3306/db_name?serverVersion=mariadb-10.5.8"

# to use postgresql:
# DATABASE_URL="postgresql://db_user:db_password@127.0.0.1:5432/db_name?serverVersion=11&charset=utf8"

```

D'autre réglages sont disponibles dans `config/packages/doctrine.yaml``


Création de la base de données :

```bash
php bin/console doctrine:database:create
```

Tout effacer et recréer la basse de données (Attention !):

```bash
php bin/console doctrine:database:drop --force
php bin/console doctrine:database:create
```

## Création d'une entité

Une entité représente le type d'objets auxquels on s'intéresse dans l'application. C'est avant tout une classe PHP. On va suivre l'exemple développé dans le  document [Databases and Doctrine](https://symfony.com/doc/current/doctrine.html) sur le site de Symfony sous licence [CC BY 3.0](http://creativecommons.org/licenses/by-sa/3.0/).

Par convention on crée les modèles  dans le sous-dossier `Entity`.


On peut créer l'entité à la main, c'est une classe php classique. On peut aussi utilsier l'utilitaire de création d'entités  :

```bash
php bin/console make:entity
```

Ce script est interactif et nous permet de définir une éntité avec ses champs. 

Ce script produit 2 fichiers :

- `src/Entity/Product.php`
- `src/Repository/RepositoryProduct.php`

Dans `Product.php` :
  - par défaut la table `product` est liée au modèle objet `Product` (c'est modifiable)
  - un champ `id` a été ajouté, c'est la clé primaire
  - les noms des colonnes portent le  nom des champs (c'est modifiable)
  - les _setters_  retournent l'objet courant. On peut faire du chainage de méthodes

```php
<?php

namespace App\Entity;

use App\Repository\ProductRepository;
use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Entity(repositoryClass=ProductRepository::class)
 */
class Product
{
    /**
     * @ORM\Id
     * @ORM\GeneratedValue
     * @ORM\Column(type="integer")
     */
    private $id;

    /**
     * @ORM\Column(type="string", length=255)
     */
    private $name;

    /**
     * @ORM\Column(type="integer")
     */
    private $price;

    // ...
}
```

`RepositoryProduct.php` : on en reparle plus tard.


## Persistance du modèle objet


Une fois le modèle défini, on peut générer la table associée dans la base de données. On appelle cela la migration. 

```bash
 php bin/console make:migration
 ```


Cette commande est très puissante, elle compare les tables et les modèles existants et génère le code SQL approprié. (e.g. utilise des "ALTER TABLE" lors de la mise a jour de modèles existants).

Le code php généré pour modifier la base de donnée se trouve dans le dossier `migrations/``


Pour exécuter la requête et effectivement migrer la base :

```bash
php bin/console doctrine:migrations:migrate
```


## Création et persistance d'objets

On a maintenant un modèle objet persistant opérationnel. On peut **créer**, **afficher**, **modifier** et **supprimer** des objets de ce type. Ce genre d'**action** se fait naturellement dans un contrôleur.

on utilise la commande suivante pour générer un contrôleur de base : 

```bash
php bin/console make:controller ProductController
```

On note :

- l'accès au "gestionnaire d'entités" (_entity manager_) via : `$this->getDoctrine()->getManager()`
- c'est l'objet qui fait réellement les requêtes
- la méthode `persist` qui indique à l'_entity manager_ que l'objet passé en paramètre doit être persisté
- la méthode `flush` exécute toutes les requêtes nécessaires en un seul _prepare_.
- on peut faire plusieurs `persist` pour un seul `flush`.


```php
<?php

namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;
use App\Entity\Product;

class ProductController extends AbstractController
{
    
    #[Route('/product/create', name: 'create_product')]
    public function create_product(): Response
    {
        // you can fetch the EntityManager via $this->getDoctrine()
        // or you can add an argument to the action: createProduct(EntityManagerInterface $entityManager)
        $entityManager = $this->getDoctrine()->getManager();

        $product = new Product();
        $product->setName('Keyboard');
        $product->setPrice(1999);
        $product->setDescription('Ergonomic and stylish!');

        // tell Doctrine you want to (eventually) save the Product (no queries yet)
        $entityManager->persist($product);

        // actually executes the queries (i.e. the INSERT query)
        $entityManager->flush();

        return new Response('Saved new product with id '.$product->getId());
    }
    
    #[Route('/product', name: 'product')]
    public function index(): Response
    {
        return $this->render('product/index.html.twig', [
            'controller_name' => 'ProductController',
        ]);
    }
}
```

On appel cette route : <https://localhost:8000/product/create>

On vérifie l'existantce de l'objet dans la base : 

```bash
php bin/console doctrine:query:sql 'SELECT * FROM product'
```

## Consulter un objet

Si l'on connait l'identifient d'un objet alors il est très simple de le consulter (_read_) à partir du contrôleur.

On effectue toujours les requêtes de consultation sur un type d'objets grâce à son  _Repository_ (`RepositoryProduct`)  : `$this->getDoctrine()->getRepository('AppBundle:Product')`.

"AppBundle:Product" est équivalent a "AppBundle\Entity\Product".


```php
    /**
     * @Route("/product/{id}", name="product_show")
     */
    public function show(int $id): Response
    {
        $product = $this->getDoctrine()
            ->getRepository(Product::class)
            ->find($id);

        if (!$product) {
            throw $this->createNotFoundException(
                'No product found for id '.$id
            );
        }

        return new Response('Check out this great product: '.$product->getName());
    }
```

On peut aussi se passer de l'utilisation du _Repository_  avec l'annotation `@ParamConverter`.


```bash
composer require sensio/framework-extra-bundle
```


```php
<?php
// src/App/Controller/ProductController.php
// ...
use Sensio\Bundle\FrameworkExtraBundle\Configuration\ParamConverter;
// ...
    /**
    * @Route("/product/{id}", name="product_show")
    * @ParamConverter("product", class="App:Product")
    */
    public function show(Product $product): Response
    {
        // use the Product!
        // ...
    }
```


## Le _Repository_

Le `Repository` contient une quantité de méthodes qui facilitent l'accès au modèle de données. On peut accéder aux objets:

- un par un par leur `id`: `find($id)`
- dynamiquement avec en fonction des champs: `findOneByName('foo')`, `findOneByPrice(19.99)`
- plusieurs à la fois : `findByPrice(19.99)`
- tous : `findAll()`
- un en fonction de plusieurs conditions :

```php
// query for one product matching by name and price
$product = $repository->findOneBy(
    array('name' => 'foo', 'price' => 19.99)
);
```
- plusieurs fonction de plusieurs conditions :

```php
// query for all products matching the name, ordered by price
$products = $repository->findBy(
    array('name' => 'foo'),
    array('price' => 'ASC')
);
```

## Mise à jour d'un objet

Une fois que l'on dispose d'une référence à un objet il est facile de le mettre à jours avec des accesseurs. Il faut ensuite appeler l'`entity manager` pour activer la persistance. On fait cela en 3 étapes.

1. On récupère l'objet a partir de doctrine (ici c'est `@ParamConverter` qui s'en charge)
2. modifier l'objet avec les modificateurs (e.g. : `setPrice`)
3. appel à la méthode  `flush()` de l'`entity manager`.

Remarque : on n'a pas besoin d'appelles `persist()` car la référence à `$product` vient de doctrine et l'entity manager sait déjà qu'il faut persister cet objet.

```php
<?php
// src/Controller/ProductController.php
// ...
  /**
   * @Route("/product/{id}/inflate", name="product_inflate")
   * @ParamConverter("product", class="App:Product")
   */
  public function inflateAction(Product $product)
  {
      $product->setPrice($product->getPrice() * 1.01);
      $em = $this->getDoctrine()->getManager();
      $em->flush();
      return $this->redirectToRoute('product_show', ['id'=>$product->getId()]);

  }
```


## Supprimer un objet

On supprime un objet avec la méthode `remove()` de l'`entity manager` :

```php
$em->remove($product);
$em->flush();
```

## Autres méthodes pour faire des requêtes

L'usage du `repository` est de loin la méthode la plus élégante pour accéder aux données persitées. Mais les méthodes proposées par défaut sont parfois insuffisantes.


### DQL
Symfony propose un langage de requête proche du SQL : le DQL. L'idée est la même que SQL sauf qu'on requête des **objets de classes** plutôt que des **tuples de tables**.

Notes :
- on utilise l'`entity manager` pour créer des requêtes.
- on utilise des _placeholder_ (`:price`) et la fonction `setParameter()` comme avec `PDO` pour évider les attaques de type injection SQL.


```php
<?php
// ...
$em = $this->getDoctrine()->getManager();
$query = $em->createQuery(
    'SELECT p
    FROM App\Entity\Product p
    WHERE p.price > :price
    ORDER BY p.price ASC'
)->setParameter('price', '19.99');

$products = $query->getResult();
// to get just one result:
// $product = $query->setMaxResults(1)->getOneOrNullResult();
```

### Doctrine Query Builder

Le `QueryBuilder` permet de créer des requêtes type DQL avec un ensemble d'appels a méthodes.

```php
<?php
// ...
$repository = $this->getDoctrine()
    ->getRepository(Product::class);

// createQueryBuilder automatically selects FROM Product
// and aliases it to "p"
$query = $repository->createQueryBuilder('p')
    ->where('p.price > :price')
    ->setParameter('price', '19.99')
    ->orderBy('p.price', 'ASC')
    ->getQuery();

$products = $query->getResult();
// to get just one result:
// $product = $query->setMaxResults(1)->getOneOrNullResult();
```


Enfin on peut insérer ces requêtes complexes dans le `repository`  et les utiliser directement dans les contrôleurs :

```php
<?php
// src/Repository/ProductRepository.php
// ...
class ProductRepository extends ServiceEntityRepository
{
    public function findAllGreaterThanPrice(int $price, bool $includeUnavailableProducts = false): array
    {
        // automatically knows to select Products
        // the "p" is an alias you'll use in the rest of the query
        $qb = $this->createQueryBuilder('p')
            ->where('p.price > :price')
            ->setParameter('price', $price)
            ->orderBy('p.price', 'ASC');

        if (!$includeUnavailableProducts) {
            $qb->andWhere('p.available = TRUE');
        }

        $query = $qb->getQuery();

        return $query->execute();
    }
}
```

```php
<?php
// src/Controller/ProductController.php
// ...

  /**
   * @Route("/products/morethan/{price}", name="product_morethan")
   */
  public function showPriceMoreThanAction($price)
  {
      $repository = $this->getDoctrine()
          ->getRepository(Product::class);
      $products = $repository->getProductsPriceMoreThan($price);

      return $this->render('product/showAll.html.twig', array(
          'products' => $products, 'title'=> "Products whose price is more than \$$price"));

  }
```


## Relations entre entités / Associations


### Relations 1-n


Supposons que chaque _product_ possède une et une seule a catégorie. On peut créer une nouvelle entité `Category` et la lier a `Product`:

```bash
php bin/console make:entity Category
```
- champ `name`, type: `string`,  taille: `256`


On utilise la commande `make:entity` pour lier les 2 entités : 

```bash
$ php bin/console make:entity

Class name of the entity to create or update (e.g. BraveChef):
> Product

New property name (press <return> to stop adding fields):
> category

Field type (enter ? to see all types) [string]:
> relation

What class should this entity be related to?:
> Category

Relation type? [ManyToOne, OneToMany, ManyToMany, OneToOne]:
> ManyToOne

Is the Product.category property allowed to be null (nullable)? (yes/no) [yes]:
> no

Do you want to add a new property to Category so that you can access/update
Product objects from it - e.g. $category->getProducts()? (yes/no) [yes]:
> yes

New field name inside Category [products]:
> products

Do you want to automatically delete orphaned App\Entity\Product objects
(orphanRemoval)? (yes/no) [no]:
> no

New property name (press <return> to stop adding fields):
>
(press enter again to finish)
```


Dans Product on note :

- l'annotation "ORM\ManyToOne"
- et son paramètre "inversedBy"

```php
<?php
// src/Entity/Product.php
namespace App\Entity;

// ...
class Product
{
    // ...

    /**
     * @ORM\ManyToOne(targetEntity="App\Entity\Category", inversedBy="products")
     */
    private $category;

    public function getCategory(): ?Category
    {
        return $this->category;
    }

    public function setCategory(?Category $category): self
    {
        $this->category = $category;

        return $this;
    }
}
```

Dans category on note :

- l'annotation "ORM\OneToMany"
- et son paramètre "mappedBy"
- le constructeur de `Category` qui initialise la propriété comme une collection.


```php
<?php
// src/Entity/Category.php

// ...
use Doctrine\Common\Collections\ArrayCollection;
use Doctrine\Common\Collections\Collection;

class Category
{
    // ...

    /**
     * @ORM\OneToMany(targetEntity="App\Entity\Product", mappedBy="category")
     */
    private $products;

    public function __construct()
    {
        $this->products = new ArrayCollection();
    }

    /**
     * @return Collection|Product[]
     */
    public function getProducts(): Collection
    {
        return $this->products;
    }

    // addProduct() and removeProduct() were also added
}
```




On n'a plus qu'à mettre à jour le schema de base de données :

```bash
 php bin/console doctrine:migrations:diff
 php bin/console doctrine:migrations:migrate
 ```

### Persister des données liées

On persiste les données liées de la même manière que les données classiques. Chaque création de nouvel objet doit être suivit d'un appel a `persist()` dans l'_entity manager_

```php
// src/Controller/ProductController.php
namespace App\Controller;

// ...
use App\Entity\Category;
use App\Entity\Product;
use Symfony\Component\HttpFoundation\Response;

class ProductController extends AbstractController
{
    /**
     * @Route("/product", name="product")
     */
    public function index(): Response
    {
        $category = new Category();
        $category->setName('Computer Peripherals');

        $product = new Product();
        $product->setName('Keyboard');
        $product->setPrice(19.99);
        $product->setDescription('Ergonomic and stylish!');

        // relates this product to the category
        $product->setCategory($category);

        $entityManager = $this->getDoctrine()->getManager();
        $entityManager->persist($category);
        $entityManager->persist($product);
        $entityManager->flush();

        return new Response(
            'Saved new product with id: '.$product->getId()
            .' and new category with id: '.$category->getId()
        );
    }
}
```


### Accéder à des objets liés

Grâce aux accesseurs définis dans les modèles il est facile d'accéder aux données liées :




```php
<?php
// ...
public function showAction($id)
{
    $product = $this->getDoctrine()
        ->getRepository(Product::class)
        ->find($id);

    $categoryName = $product->getCategory()->getName();
    //...

```

On note ici que l'objet `Category` est "_lazily loaded_", il est chargé dans une seconde requête, quand `getName` est appelé.


### Jointures

Si on sait d'avance que ces 2 requêtes vont être faites, on peut avoir recours à une **jointure**

```php
<?php
// src/AppBundle/Entity/ProductRepository.php
//...
  public function findOneByIdJoinedToCategory(int $productId): ?Product
    {
        $entityManager = $this->getEntityManager();

        $query = $entityManager->createQuery(
            'SELECT p, c
            FROM App\Entity\Product p
            INNER JOIN p.category c
            WHERE p.id = :id'
        )->setParameter('id', $productId);

        return $query->getOneOrNullResult();
    }
```

puis utiliser le _repository_ dans les contrôleurs :

```php
public function show(int $id): Response
{
    $product = $this->getDoctrine()
        ->getRepository(Product::class)
        ->findOneByIdJoinedToCategory($id);

    $category = $product->getCategory();

    // ...
}
```

### Relations n-n

Plus d'information dans la [documentation de Doctrine](http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/reference/association-mapping.html).


```php
<?php
/** @Entity */
class User
{
    // ...

    /**
     * @ManyToMany(targetEntity="Group", inversedBy="users")
     * @JoinTable(name="users_groups")
     */
    private $groups;

    public function __construct() {
        $this->groups = new \Doctrine\Common\Collections\ArrayCollection();
    }

    // ...
}

/** @Entity */
class Group
{
    // ...
    /**
     * @ManyToMany(targetEntity="User", mappedBy="groups")
     */
    private $users;

    public function __construct() {
        $this->users = new \Doctrine\Common\Collections\ArrayCollection();
    }

    // ...
}
```

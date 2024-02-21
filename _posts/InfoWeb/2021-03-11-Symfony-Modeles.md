---
layout: post
title: Gestion des modèles de données avec Symfony
categories:
- InfoWeb
- lecture
author: Yoann Pigné
published: true
update: 2024-02-21
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

Symfony seul ne permet pas de gérer des modèles objets ni de se connecter à une base de données. On utilise **Doctrine**, un ORM (*Object-Relational Mapping*) pour mettre en concordance des objets PHP avec un modèle persistant (Base de données relationnelle).

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

Ce script est interactif et nous permet de définir une entité avec ses champs. On se laisse quider pour ajouter un champ `name` de type `string` et un `price`de type `integer`.

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

#[ORM\Entity(repositoryClass: ProductRepository::class)]
class Product
{
    #[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column]
    private ?int $id = null;

    #[ORM\Column(length: 255)]
    private ?string $name = null;

    #[ORM\Column]
    private ?int $price = null;

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

On peut modifier une entité et ainsi créer une nouvelle migration. Ajouter un champ `description`de type `text`, puis faire la migration pour appliquer les modification à la base de données : 
```
php bin/console make:entity product
# ...
php bin/console make:migration
# on note les requetes SQL du type ALTER TABLE
php bin/console doctrine:migrations:migrate
```



## Création et persistance d'objets

On a maintenant un modèle objet persistant opérationnel. On peut **créer**, **afficher**, **modifier** et **supprimer** des objets de ce type. Ce genre d'**action** se fait naturellement dans un contrôleur.

on utilise la commande suivante pour générer un contrôleur de base : 

```bash
php bin/console make:controller ProductController
```

On note :

- l'accès au "gestionnaire d'entités" (_entity manager_) via le paramètre de l'action : `EntityManagerInterface $entityManager`
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
use Doctrine\ORM\EntityManagerInterface;

class ProductController extends AbstractController
{
    
    #[Route('/product', name: 'create_product')]
    public function createProduct(EntityManagerInterface $entityManager): Response
    {
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
    
}
```

On appel cette route : <https://localhost:8000/product>

On vérifie l'existence de l'objet dans la base : 

```bash
php bin/console doctrine:query:sql 'SELECT * FROM product'
```

## Consulter un objet

Si l'on connait l'identifient d'un objet alors il est très simple de le consulter (_read_) à partir du contrôleur.

On effectue toujours les requêtes de consultation sur un type d'objets grâce à son  _Repository_ (`RepositoryProduct`)  : `$entityManager->getRepository(Product::class)`.


```php
// src/App/Controller/ProductController.php
// ...
    #[Route('/product/{id}', name: 'product_show')]
    public function show(EntityManagerInterface $entityManager, int $id): Response
    {
        $product = $entityManager->getRepository(Product::class)->find($id);

        if (!$product) {
            throw $this->createNotFoundException(
                'No product found for id '.$id
            );
        }

        return new Response('Check out this great product: '.$product->getName());
    }
```



## Le _Repository_

Le `Repository` contient une quantité de méthodes qui facilitent l'accès au modèle de données. On peut accéder aux objets:

- un par un, par leur `id`: `find($id)`
- dynamiquement avec en fonction des champs: `findOneBy(['name' => 'Keyboard']);`, `findOneBy(['price' => 1999]);`
- plusieurs à la fois avec plusieurs critères : `findBy(['name' => 'Keyboard' , 'price' => 1999]);`
- tous : `findAll()`

```php
// query for one product matching by name and price
$product = $repository->findOneBy(
    ['name' => 'foo', 'price' => 19.99]
);
```
- plusieurs fonction de plusieurs conditions :

```php
// query for all products matching the name, ordered by price
$products = $repository->findBy(
    ['name' => 'foo'],
    ['price' => 'ASC']
);
```


## EntityValueResolver

Dans bien des cas on peut résoudre les entités automatiquement à partir de l'identifiant. On peut alors utiliser le _EntityValueResolver_ pour passer directement l'objet en paramètre de la méthode du contrôleur.

```php
// src/Controller/ProductController.php
// ...
    #[Route('/product/{id}', name: 'product_show')]
    public function show(Product $product): Response
    {
        return new Response('Check out this great product: '.$product->getName());
    }
```



## Mise à jour d'un objet

Une fois que l'on dispose d'une référence à un objet il est facile de le mettre à jours avec des accesseurs. Il faut ensuite appeler l'`entity manager` pour activer la persistance. On fait cela en 3 étapes.

1. On récupère l'objet a partir de doctrine avec le _repository_.
2. On modifie l'objet avec les modificateurs (e.g. : `setPrice`)
3. On appel à la méthode  `flush()` de l'`entity manager`.

Remarque : on n'a pas besoin d'appelles `persist()` car la référence à `$product` vient de doctrine et l'entity manager sait déjà qu'il faut persister cet objet.

```php
<?php
// src/Controller/ProductController.php
// ...
    #[Route('/product/{id}/inflate', name: 'product_inflate')]
    public function update(EntityManagerInterface $entityManager, int $id): Response
    {
        $product = $entityManager->getRepository(Product::class)->find($id);

        if (!$product) {
            throw $this->createNotFoundException(
                'No product found for id '.$id
            );
        }

        $product->setPrice($product->getPrice() * 1.01);
        $entityManager->flush();

        return $this->redirectToRoute('product_show', [
            'id' => $product->getId()
        ]);
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
- on utilise des _placeholder_ (`:price`) et la fonction `setParameter()` comme avec `PDO` pour éviter les injections SQL.


```php
<?php
// ...
$entityManager = $this->getEntityManager();

$query = $entityManager->createQuery(
    'SELECT p
    FROM App\Entity\Product p
    WHERE p.price > :price
    ORDER BY p.price ASC'
)->setParameter('price', $price);

// returns an array of Product objects
return $query->getResult();
// to get just one result:
// $product = $query->setMaxResults(1)->getOneOrNullResult();
```

### Doctrine Query Builder

Le `QueryBuilder` permet de créer des requêtes type DQL avec un ensemble d'appels a méthodes.

```php
<?php
// ...
$repository = $doctrine->getRepository(Product::class);

// createQueryBuilder automatically selects FROM Product
// and aliases it to "p"
$query = $repository->createQueryBuilder('p')
    ->where('p.price > :price')
    ->setParameter('price', 19.99)
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
    public function findAllGreaterThanPrice(int $price): array
    {
        // automatically knows to select Products
        // the "p" is an alias you'll use in the rest of the query
        $qb = $this->createQueryBuilder('p')
            ->where('p.price > :price')
            ->setParameter('price', $price)
            ->orderBy('p.price', 'ASC');

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
  public function showPriceMoreThanAction(ManagerRegistry $doctrine, $price)
  {
      $repository = $doctrine->getRepository(Product::class);
      $products = $repository->findAllGreaterThanPrice($price);

      return $this->render('product/some.html.twig', array(
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


    #[ORM\ManyToOne(targetEntity: Category::class, inversedBy: 'products')]
    #[ORM\JoinColumn(nullable: false)]
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

Dans `Category` on note :

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

    #[ORM\OneToMany(mappedBy: 'category', targetEntity: Product::class)]
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
use Doctrine\ORM\EntityManagerInterface;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

class ProductController extends AbstractController
{
    #[Route('/product', name: 'product')]
    public function index(EntityManagerInterface $entityManager): Response
    {
        $category = new Category();
        $category->setName('Computer Peripherals');

        $product = new Product();
        $product->setName('Keyboard');
        $product->setPrice(19.99);
        $product->setDescription('Ergonomic and stylish!');

        // relates this product to the category
        $product->setCategory($category);

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
class ProductController extends AbstractController
{
    public function show(ProductRepository $productRepository, int $id): Response
    {
        $product = $productRepository->find($id);
        // ...

        $categoryName = $product->getCategory()->getName();

        // ...
    }
}

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
    public function show(ProductRepository $productRepository, int $id): Response
    {
        $product = $productRepository->findOneByIdJoinedToCategory($id);

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

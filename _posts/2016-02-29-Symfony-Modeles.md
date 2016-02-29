---
layout: page
title: Gestion des modèles de données avec Symfony
categories:
- InfoWeb
- lecture
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
- Connection du modèle objet à la base de données
- **Création** / **Lecture** / **Modification** / **Suppression** de données persistantes
- Utilisation des `Repository`
- Jointures entre modèles

Ce que l'on verra plus tard:

- Génaration de formulaires à partir de modèles objets
- Validation de formulaires
- Gestion des utilisateurs et des droits dans une application Symfony


## Configuration

Symfony seul ne permet pas de gérer des modèles objets ni de se connecter à une base de données. On utilise **Doctrine**, un ORM pour mettre en concordance des objets PHP avec un modèle persistant (Base de données).

Doctrine est présent dans l'installation par défaut que l'on utilise.

On Configure doctrine pour se connecter à la BADO via les 2 fichiers

  - `app/config/parameters.yml`
  - `app/config/config.yml`

```yaml
# app/config/parameters.yml
parameters:
    database_host: 127.0.0.1
    database_port: null
    database_name: symfony1
    database_user: db_user
    database_password: pnCFseEZI4O4joj3d76JhdjTy18gD
```

```yaml
# app/config/parameters.yml
# ...
# Doctrine Configuration
doctrine:
    dbal:
        driver:   pdo_pgsql # ou pdo_mysql
        host:     "%database_host%"
        port:     "%database_port%"
        dbname:   "%database_name%"
        user:     "%database_user%"
        password: "%database_password%"
        charset:  UTF8
        # if using pdo_sqlite as your database driver:
        #   1. add the path in parameters.yml
        #     e.g. database_path: "%kernel.root_dir%/data/data.db3"
        #   2. Uncomment database_path in parameters.yml.dist
        #   3. Uncomment next line:
        #     path:     "%database_path%"

    orm:
        auto_generate_proxy_classes: "%kernel.debug%"
        naming_strategy: doctrine.orm.naming_strategy.underscore
        auto_mapping: true
```

Creation de la base de données :

```bash
php bin/console doctrine:database:create
```

## Création d'une entité

Une entité représente le type d'objets auxquels on s'intéresse dans l'application. C'est avant tout une classe PHP. On va suivre l'exemple développé sur le site de Symfony avec l'entité `Product`.

Par convention on crée les Modèles  dans ls sous-dossier `Entity` du _bundle_ courant.

```php
<?php
// src/AppBundle/Entity/Product.php
namespace AppBundle\Entity;

class Product
{
    protected $name;
    protected $price;
    protected $description;
}
```


Cette classe est bien un modèle objet (une _entité_) mais elle ne peut pas encore persister en base de données.

Pour que l'objet soit persistant il faut **lier les champs de la classe aux colonnes d'une table de base de données**.

Doctrine lie les objets aux bases de données grace à des paramètres (annotation, fichiers de configuration...)

```bash
 php bin/console doctrine:generate:entity
```

- 2 fichiers sont générés :
  - `src/AppBundle/Entity/Product.php`
  - `src/AppBundle/Repository/RepositoryProduct.php`
- Dans `Product.php` :
  - par défaut la table `product` est liée au modèle objet `Product` (c'est modifiable)
  - un champ `id` a été ajouté, c'est la clé primaire
  - les noms des colonnes protent le  nom des champs (c'est modifiable)
  - les _setters_  retournent l'objet courant. On peut faire du chainage de méthodes



```php
<?php
// src/AppBundle/Entity/Product.php

namespace AppBundle\Entity;

use Doctrine\ORM\Mapping as ORM;

/**
 * Product
 *
 * @ORM\Table(name="product")
 * @ORM\Entity(repositoryClass="AppBundle\Repository\ProductRepository")
 */
class Product
{
    /**
     * @var int
     *
     * @ORM\Column(name="id", type="integer")
     * @ORM\Id
     * @ORM\GeneratedValue(strategy="AUTO")
     */
    private $id;

    /**
     * @var string
     *
     * @ORM\Column(name="name", type="string", length=100)
     */
    private $name;

    /**
     * @var float
     *
     * @ORM\Column(name="price", type="decimal")
     */
    private $price;

    /**
     * @var string
     *
     * @ORM\Column(name="description", type="text")
     */
    private $description;


    /**
     * Get id
     *
     * @return int
     */
    public function getId()
    {
        return $this->id;
    }

    /**
     * Set name
     *
     * @param string $name
     *
     * @return Product
     */
    public function setName($name)
    {
        $this->name = $name;

        return $this;
    }

    /**
     * Get name
     *
     * @return string
     */
    public function getName()
    {
        return $this->name;
    }

    /**
     * Set price
     * ...
     */

    /**
     * Get price
     * ...
     */

    /**
     * Set description
     * ...
     */

    /**
     * Get description
     * ...
     */
}
```

Dans `RepositoryProduct.php` :

```php
<?php
// src/AppBundle/Repository/RepositoryProduct.php
namespace AppBundle\Repository;

/**
 * ProductRepository
 *
 * This class was generated by the Doctrine ORM. Add your own custom
 * repository methods below.
 */
class ProductRepository extends \Doctrine\ORM\EntityRepository
{
}
```


## Persistance du modèle objet


Une fois le modèle défini, on peut générer la table associée dans la base de données. Pour cela on utilise la commande `doctrine:schema:update`.

Cette commande est très puissante, elle compare les tables et les modèles existants et génère le code SQL approprié. (e.g. utilise des "ALTER TABLE" lors de la mise a jour de modèles existants).


Pour voir la commande SQL qui serait exécutée :

```bash
php bin/console doctrine:schema:update --dump-sql
```

```sql
CREATE SEQUENCE product_id_seq INCREMENT BY 1 MINVALUE 1 START 1;
CREATE TABLE product (id INT NOT NULL, name VARCHAR(100) NOT NULL, price NUMERIC(10, 0) NOT NULL, description TEXT NOT NULL, PRIMARY KEY(id));
```


Pour exécuter la requête :

```bash
php bin/console doctrine:schema:update --force
```


## Creation et persistance d'objets

On a maintenant un modèle objet persistant opérationnel. On peut **créer**, **afficher**, **modifier** et **supprimer** des objets de ce type. Ce genre d'**action** se fait naturellement dans un contrôleur.


On note :

- l'accès au "gestionnaire d'entités" (_entity manager_) via : `$this->getDoctrine()->getManager()`
- c'est l'objet qui fait réellement les requêtes
- la méthode `persist` qui indique à l'_entity manager_ que l'objet passé en paramètre doit être persisté
- la méthode `flush` exécute toutes les requêtes nécessaires en un seul _prepare_.
- on peut faire plusieurs `persist` pous un seul `flush`.


```php
<?php
// src/AppBundle/Controller/ProductController.php

namespace AppBundle\Controller;

use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
use Symfony\Bundle\FrameworkBundle\Controller\Controller;
use Symfony\Component\HttpFoundation\Response;
use AppBundle\Entity\Product;

class ProductController extends Controller
{
    /**
     * @Route("/product/create", name="product_create")
     */
    public function createAction()
    {

    $product = new Product();
    $product->setName('A Foo Bar');
    $product->setPrice('19.99');
    $product->setDescription('Lorem ipsum dolor');

    $em = $this->getDoctrine()->getManager();

    $em->persist($product);
    $em->flush();

    return new Response('Created product id '.$product->getId());
}
```

## Consulter un objet

Si l'on connait l'identifient d'un objet alors il est très simple de le consulter (_read_) à partir du contrôleur.

On effectue toujours les requêtes de consultation sur un type d'objets grâce à son  _Repository_ (`RepositoryProduct`)  : `$this->getDoctrine()->getRepository('AppBundle:Product')`.

"AppBundle:Product" est équivalent a "AppBundle\Entity\Product".


```php
<?php
// src/AppBundle/Controller/ProductController.php
// ...

  /**
  * @Route("/product/{id}", name="product_show")
  */
  public function showAction($id)
  {
      $product = $this->getDoctrine()
          ->getRepository('AppBundle:Product')
          ->find($id);

      if (!$product) {
          throw $this->createNotFoundException(
              'No product found for id '.$id
          );
      }

      // ... do something, like pass the $product object into a template
  }
```

On peut aussi se passer de l'utilisation du _Repository_  avec l'annotation `@ParamConverter`.

```php
<?php
// src/AppBundle/Controller/ProductController.php
// ...
use Sensio\Bundle\FrameworkExtraBundle\Configuration\ParamConverter;
// ...
  /**
   * @Route("/product/{id}", name="product_show")
   * @ParamConverter("product", class="AppBundle:Product")
   */
   public function showAction(Product $product)
   {
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

Une fois que l'on dispose d'une référence à un objet il est facile de le mettre à jours avec des accesseurs. Il faut ensuite appeller l'`entity manager` pour activer la persistance. On fait cela en 3 étapes.

1. On récupère l'objet a partir de doctrine (ici c'est `@ParamConverter` qui s'en charge)
2. modifier l'objet avec les modificateurs (e.g. : `setPrice`)
3. appel à la méthode  `flush()` de l'`entity manager`.

Remarque : on n'a pas besoin d'appeller `persist()` car la référence à `$product` vient de doctrine et l'entity manager sait déjà qu'il faut persister cet objet.

```php
<?php
// src/AppBundle/Controller/ProductController.php
// ...
  /**
   * @Route("/product/{id}/inflate", name="product_inflate")
   * @ParamConverter("product", class="AppBundle:Product")
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

On supprime un objet avec la methode `remove()` de l'`entity manager` :

```php
$em->remove($product);
$em->flush();
```

## Autres méthodes pour faire des requêtes

L'usage du `repository` est de loin la méthhode la plus élégante pour accéder aux données persitées. Mais les méthodes proposées par défaut sont parfois insuffisantes.


### DQL
Symfony propose un langage de requête proche du SQL : le DQL. L'idée est la même que SQL sauf qu'on requête des **objets de classes** plutot que des **tuples de tables**.

Notes :
- on utilise l'`entity manager` pour créer des requêtes.
- on utilise des _placeholder_ (`:price`) et la fonction `setParameter()` comme avec `PDO` pour évider les attaques de type injection SQL.


```php
<?php
// ...
$em = $this->getDoctrine()->getManager();
$query = $em->createQuery(
    'SELECT p
    FROM AppBundle:Product p
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
    ->getRepository('AppBundle:Product');

// createQueryBuilder automatically selects FROM AppBundle:Product
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
// src/AppBundle/Repository/ProductRepository.php

namespace AppBundle\Repository;

class ProductRepository extends \Doctrine\ORM\EntityRepository
{
    public function getProductsMoreExpensiveThan($price){
        $query = $this->createQueryBuilder('p')
            ->where('p.price > :price')
            ->setParameter('price', $price)
            ->orderBy('p.price', 'ASC')
            ->getQuery();

        return $query->getResult();
    }
}
```

```php
<?php
// src/AppBundle/Controller/ProductController.php
// ...

  /**
   * @Route("/products/morethan/{price}", name="product_morethan")
   */
  public function showPriceMoreThanAction($price)
  {
      $repository = $this->getDoctrine()
          ->getRepository('AppBundle:Product');
      $products = $repository->getProductsPriceMoreThan($price);

      return $this->render('product/showAll.html.twig', array(
          'products' => $products, 'title'=> "Products whose price is more than \$$price"));

  }
```


## Realtions entre entités / Associations


### Relations 1-n


Supposons que chaque _product_ possède une ét une seule a catégorie. On peut créer une nouvelle entiter et la lier a `Product`:

```bash
php bin/console doctrine:generate:entity --no-interaction \
    --entity="AppBundle:Category" \
    --fields="name:string(255)"
```

On crée une propriété `product` dnas la classe `Category`.

On note :

- l'annotation "ORM\OneToMany"
- et son paramètre "mappedBy"
- le constructeur de `Category` qui initialise la propriété comme une collection.


```php
<?php
// src/AppBundle/Entity/Category.php

// ...
use Doctrine\Common\Collections\ArrayCollection;

class Category
{
    // ...

    /**
     * @ORM\OneToMany(targetEntity="Product", mappedBy="category")
     */
    protected $products;

    public function __construct()
    {
        $this->products = new ArrayCollection();
    }
}
```

Ensuite on ajour la catérogie unique a chaque `Product`.

On note :

- l'annotation "ORM\ManyToOne"
- et son paramètre "inversedBy"

```php
<?php
// src/AppBundle/Entity/Product.php

// ...
class Product
{
    // ...

    /**
     * @ORM\ManyToOne(targetEntity="Category", inversedBy="products")
     * @ORM\JoinColumn(name="category_id", referencedColumnName="id")
     */
    protected $category;
}

On demande a Doctrine de générer les getters et setters manquants :

```bash
php bin/console doctrine:generate:entities AppBundle
```


Mise a jour du schema de base de données :

```bash
php bin/console doctrine:schema:update --force
```

### Persister des donénes liées

On persiste les données liées de la même manière que les données classiques. Chaque création de nouvel objet doit être suivit d'un appel a `persist()` dans l'_entity manager_

```php
  public function createProductAction()
  {
    $category = new Category();
    $category->setName('Main Products');

    $product = new Product();
    $product->setName('Foo');
    $product->setPrice(19.99);
    $product->setDescription('Lorem ipsum dolor');
    // relate this product to the category
    $product->setCategory($category);

    $em = $this->getDoctrine()->getManager();
    $em->persist($category);
    $em->persist($product);
    $em->flush();

    return new Response(
        'Created product id: '.$product->getId()
        .' and category id: '.$category->getId()
    );
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
        ->getRepository('AppBundle:Product')
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
  public function findOneByIdJoinedToCategory($id)
  {
      $query = $this->getEntityManager()
          ->createQuery(
              'SELECT p, c FROM AppBundle:Product p
              JOIN p.category c
              WHERE p.id = :id'
          )->setParameter('id', $id);

      try {
          return $query->getSingleResult();
      } catch (\Doctrine\ORM\NoResultException $e) {
          return null;
      }
  }
```

puis utiliser le _repository_ dans les contôleurs :

```php
public function showAction($id)
{
    $product = $this->getDoctrine()
        ->getRepository('AppBundle:Product')
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

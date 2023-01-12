---
layout: post
title: Gestion des Formulaires avec Symfony
categories:
- InfoWeb
- lecture
author: Yoann Pigné
published: false
update: 2022-02-24
---

Récapitulatif des cours et TPs précédents :


- Représentation d'un modèle objet avec `Doctrine`
- Conception d'un modèle objet
- Persistance d'un modèle objet dans une base de données
- **Création** / **Lecture** / **Modification** / **Suppression** de données
- Requêtes de lecture avec les `Repository`
- Requêtes de mise à jour (écriture) avec l'`Entity Manager`
- Jointures entre modèles

Aujourd'hui :

- Génération de formulaires à partir de modèles objets
- Validation de formulaires
- Gestion des utilisateurs et des droits dans une application Symfony


## Génération de formulaire à partir d'une entité

Symfony utilise plusieurs objets pour générer des formulaires. La classe la plus importante est `Form`. Un objet de type `Form` gère pour nous plusieurs choses:

- les types de requêtes (`GET`, `POST`)
- l'upload de fichiers
- la protection contre les CSRF (_Cross-Site-Request-Forgery_)
- la génération de template html (e.g. associer un `<input type="...">` à un champ de l'entité)
- la traduction de messages d'erreur et autres étiquettes
- la validation lors de la soumission des données pour créer/modifier des entités

On l'installe avec cette commande : 

```bash
composer require symfony/form
```


Les 2 fonctions d'un formulaire :

- donner une représentation visuelle (une vue) d'un objet (entité)
- transformer les entrées utilisateur (input) en objet (entité)

### Représentation d'un objet

La représentation se fait en 2 étapes.

1. La création d'un objet Symfony `Form`
2. La création d'une vue (TWIG) à partir du formulaire


On note :

- la route constituée en 2 parties (`/musee` +  `/{id}/dummy` =  `/musee/{id}/dummy`)
- les **types** utilisés pour créer le formulaire : <https://symfony.com/doc/current/reference/forms/types.html>
- la méthode `createView()` du formulaire pour la création du template


```php
<?php
// src/Controller/MuseeController.php

namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\HttpFoundation\Request;

use Symfony\Component\Routing\Annotation\Route;
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Method;

use App\Entity\Musee;

use Symfony\Component\Form\Extension\Core\Type\SubmitType;
use Symfony\Component\Form\Extension\Core\Type\TextType;
use Symfony\Component\Form\Extension\Core\Type\NumberType;

/**
 *
 * @Route("/musee")
 */
class MuseeController extends AbstractController
{

  // ...

  /**
   * @Route("/{id}/dummy", name="musee_dummy")
   */
  public function dummyAction(Musee $musee): Response
  {
     $dummyForm = $this->createFormBuilder($musee)
        ->add('nom', TextType::class)
        ->add('adresse', TextType::class)
        ->add('longitude', NumberType::class)
        ->add('latitude', NumberType::class)
        ->add('save', SubmitType::class, array('label' => 'OK.  Ou pas...'))
        ->getForm();
     return $this->render('musee/dummy.html.twig', array(
        'form' => $dummyForm->createView(),
     ));
  }

  // ...

}
```


Plusieurs fonctions TWIG permettent de générer des formulaires. On utilise la plus simple : `form` qui génère tout le formulaire :
- la balise `<form>`  et les attributs `action` et `method`,
- les `input`,
- le champs caché CSRF,
- la fin du `</form>`


Template  minimal  :
{%raw%}
```liquid
{# dummy.html.twig #}
{% extends 'base.html.twig' %}

{% block body %}
    <h1>Dummy Musee</h1>

    {{ form(form) }}

{% endblock %}
```
{%endraw%}

Template avec plus de contrôle (<https://symfony.com/doc/current/form/form_customization.html>) et de mise en page :

- `form_label(form.truc)` affiche le _label_ du champs `truc`
- `form_error(form.truc)` affiche les erreurs de validation liées au champs `truc`
- `form_widget(form.truc)` affiche la balise HTML liée au champs `truc` (`input`, `textarea`, `select`, _etc_. )
- `form_row(form.truc)` affiche le bloc (_label_, _error_, _widget_) pour le champ `truc`.

{%raw%}
```liquid
{% extends 'base.html.twig' %}
{% block body %}

<h1>Creation d'un Musée</h1>
<p class="pull-right">
    <a href="{{ path('musee') }}"><span class="glyphicon glyphicon-th-list"></span> Retour à la liste</a>
</p>

{{ form_start(form) }}
{{ form_errors(form) }}

    {{ form_row(form.nom) }}
    {{ form_row(form.adresse) }}
    <div class="row">
        <div class="col">
            {{ form_row(form.longitude) }}
        </div>

        <div class="col">
            {{ form_row(form.latitude) }}
        </div>
    </div>

    {{ form_row(form.save, { 'label': 'Créer' }) }}
{{ form_end(form) }}

{% endblock %}
```
{%endraw%}

On peut aussi utiliser des thèmes de templates pour les formulaires. On les configurent dans le fichier `config/config/config.yml` :

```yml
# config/packages/twig.yaml
twig:
    form_themes: ['bootstrap_4_layout.html.twig']
# ...
```

### Gérer une requête de formulaire

On a affiché un formulaire à partir d'un objet existant. Maintenant on veut récupérer les données venant de l'utilisateur pour créer ou modifier des entités.

Le comportement par défaut d'une action qui gère les formulaires est de fonctionner avec les requêtes `GET` et `POST`.

La méthode `handleRequest()` du la classe `Form` permet de récupérer les valeurs des champs dans les inputs du formulaire.

La méthode `isSubmitted()` de la classe `Form` permet de savoir si on est effectivement en méthode `POST`.

La méthode `isValid()` permet de valider les données saisies.

On note que la méthode `handleRequest` est toujours appelée **avant** la méthode `createView` du formulaire. Ceci pour permettre l'affichage des erreurs de validation dans la vue.

```php
<?php
// ...
/**
 *
 * @Route("/dummynew", name="musee_dummynew")
 * @Method({"GET", "POST"})
 */
public function dummyNewAction(Request $request): Response
{
    $musee = new Musee();
    $dummyForm = $this->createFormBuilder($musee)
        ->add('nom', TextType::class)
        ->add('adresse', TextType::class)
        ->add('longitude', NumberType::class)
        ->add('latitude', NumberType::class)
        ->add('save', SubmitType::class, array('label' => 'Enregistrer'))
        ->getForm();

    $dummyForm->handleRequest($request);

    if ($dummyForm->isSubmitted() && $dummyForm->isValid()) {
        $em = $this->getDoctrine()->getManager();
        $em->persist($musee);
        $em->flush();

        return $this->redirectToRoute('musee_dummy', array('id' => $musee->getId()));
    }

    return $this->render('musee/dummy.html.twig', array(
        'musee' => $musee,
        'form' => $dummyForm->createView(),
    ));
}
```

## Validation

Avant d'enregistrer les données saisies dans le formulaire, on veut les contrôler. S'assurer qu'elle répondent à certains critères.

C'est lors de la définition de l'entité (le modèle objet) que l'on définie ces critères, avec des annotations.

<a href="https://symfony.com/doc/current/validation.html">Documentation sur la validation.</a>


{%raw%}

```php
<?php
// src/AppBundle/Entity/Musee.php
// ...

use Symfony\Component\Validator\Constraints as Assert;
//...

    /**
    * @var string
    *
    * @Assert\NotBlank()
    *
    * @ORM\Column(name="nom", type="string", length=255)
    */
    private $nom;  


    /**
     * @ORM\Column(type="string", length=1023, nullable=true)
     */
    private $adresse;

    /**
     * @ORM\Column(type="float")
     * 
     * @Assert\Range(
     *      min = -90,
     *      max = 90,
     *      notInRangeMessage = "A latitude value must be within  {{ min }} and {{ max }} degrees",
     * )
     */
     
    private $latitude;

    /**
     * @ORM\Column(type="float")
     *
     * @Assert\Range(
     *      min = 0,
     *      max = 180,
     *      notInRangeMessage = "A longitude value must be within  {{ min }} and {{ max }} degrees",
     * )
     */
    private $longitude;
```
{%endraw%}


## Classe dédiée de création de formulaire

Pour plus de clarté et de réutilisabilité du code, on va utiliser des classes dédiées pour la création de formulaires en fonction des entités. On enregistre ces classes dans un dossier dédié (`src/Form/`). On définit en fait un _type_ pour l’entité, utilisable par un formulaire.

On utilise un script de console pour aller plus vite : 

```bash
php bin/console make:form
```

Tout comme le type `TextType` est reconnu par la classe Form (avec un `<input type="text">`, etc.) notre entité `Musee` va avoir un type qui lui est propre. avec des _inputs_, des _seletcs_, _etc._


On note :

- que la classe hérite de `AbstractType`
- que 2 méthodes sont nécessaires `buildForm` et `configureOptions`
- que la méthode `configureOptions` donne accès à l'entité `Musee`
- que les appel a `add` sont par défaut mais qu'on peut les modifier en fonction des besoins de validation de bornes de valeurs par défaut, d'affichage/présentation par défaut, etc.


```php
<?php
// src/Form/MuseeType.php

<?php

namespace App\Form;

use App\Entity\Musee;
use Symfony\Component\Form\AbstractType;
use Symfony\Component\Form\FormBuilderInterface;
use Symfony\Component\OptionsResolver\OptionsResolver;
use Symfony\Component\Form\Extension\Core\Type\TextType;
use Symfony\Component\Form\Extension\Core\Type\SubmitType;

class MuseeType extends AbstractType
{
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder
            ->add('nom')
            ->add('adresse',TextType::class,['required' => false])
            ->add('latitude')
            ->add('longitude')
            ->add('save', SubmitType::class)

        ;
    }

    public function configureOptions(OptionsResolver $resolver)
    {
        $resolver->setDefaults([
            'data_class' => Musee::class,
        ]);
    }
}
```

Ensuite il suffit de modifier le contrôleur :

```php
<?php
// src/Controller/MuseeController.php


// ...

use App\Form\MuseeType;

// ...

/**
  * Creates a new Musee entity.
  *
  * @Route("/new", name="musee_new")
  * @Method({"GET", "POST"})
  */
 public function newAction(Request $request)
 {
     $musee = new Musee();
     $form = $this->createForm(MuseeType::class, $musee);
     $form->handleRequest($request);

     // ...
```



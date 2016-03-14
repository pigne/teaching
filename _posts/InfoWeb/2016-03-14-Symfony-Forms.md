---
layout: post
title: Gestion des Formulaires avec Symfony
categories:
- InfoWeb
- lecture
author: Yoann Pigné
---

Récapitulatif des cours et TPs précédents :


- Représentation d'un modèle objet avec `Doctrine`
- Conception d'un modèle objet
- Persistance d'un modèle objet dans une base de données
- **Création** / **Lecture** / **Modification** / **Suppression** de données
- Requêtes de lecture avec les 'Repository`
- Requêtes de mise à jour (écriture) avec l''Entity Manager'
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
- la traduction de messages d'erreur et autres étiquètes
- la validation lors de la soumission des données pour créer/modifier des entités



les 2 fonctions d'un formulaire :

- donner une représentation visuelle d'un objet (entité)
- gérer/transformer les entrées d'un utilisateur (input) en objet (entité)

### Représentation d'un objet

La représentation se fait en 2 étapes.

1. La création d'un objet Symfony `Form`
2. La création d'une vue (TWIG) à partir du formulaire


On note :

- la route constituée en 2 parties (`/musee` +  `/{id}/dummy` =  `/musee/{id}/dummy`)
- l'annotation `Method`
- la variable $musee créée sans utiliser le `ParamConverter`
- les types utilisés pour créer le formulaire
- la méthode `createView()` du formulaire pour la création du template


```php
<?php
// src/appBundle/Controller/MuseeController.php
// ...
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Method;
// ...
use Symfony\Component\Form\Extension\Core\Type\TextType;
use Symfony\Component\Form\Extension\Core\Type\CollectionType;
use Symfony\Component\Form\Extension\Core\Type\SubmitType;
// ...

/**
 *
 * @Route("/musee")
 */
class MuseeController extends Controller
{

  /**
  * @Route("/{id}/dummy", name="musee_demi")
  * @Method({"GET"})
  */
  public function dummyAction(Request $request, Musee $musee)
  {
     $dummyForm = $this->createFormBuilder($musee)
         ->add('nom', TextType::class)
         ->add('coordonnees', CollectionType::class)
         ->add('save', SubmitType::class, array('label' => 'OK. Ou pas...'))
         ->getForm();
     return $this->render('musee/dummy.html.twig', array(
         'form' => $dummyForm->createView(),
     ));
  }
}
```

On note plusieurs fonctions TWIG permettant de générer le formulaire:

- `form_start` gère la balise `<form>`  et les attributs `action` et `method`
- `form_widget` génère tous les `input`
- `form_end` gère le champs caché CSRF et ferme le `<form>`


Template avec un minimum de contrôle :
{%raw%}
```liquid
{# dummy.html.twig #}
{% extends 'base.html.twig' %}

{% block body %}
    <h1>Dummy Musee</h1>

    {{ form_start(form) }}
        {{ form_widget(form) }}
    {{ form_end(form) }}

{% endblock %}
```
{%endraw%}

Template avec plus de contrôle :

- `form_label(form.truc)` affiche le _label_ du champs `truc`
- `form_error(form.truc)` affiche les erreurs de validation liées au champs `truc`
- `form_widget(form.truc)` affiche la belise HTML liée au champs `truc` (`input`, `textarea`, `select`, _etc_. )
- `form_row(form.truc)` affiche le bloc (_label_, _error_, _widget_) pour le champ `truc`.

{%raw%}
```liquid
{{ form_start(form) }}
    {{ form_errors(form) }}

    <div>
        {{ form_label(form.nom) }}
        {{ form_errors(form.nom) }}
        {{ form_widget(form.nom) }}
    </div>

    <div>
        {{ form_row(form.coordonnees) }}
    </div>

    <div>
        {{ form_widget(form.save) }}
    </div>

{{ form_end(form) }}
```
{%endraw%}

On peut aussi utiliser des thèmes de templates pour les formulaires. On les configurent dans le fichier `app/config/config.yml` :

```yml
# ...
twig:
    form_themes:
        - "bootstrap_3_layout.html.twig"
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
public function dummyNewAction(Request $request)
{
    $musee = new Musee();
    $dummyForm = $this->createFormBuilder($musee)
        ->add('nom', TextType::class)
        ->add('coordonnees', CollectionType::class)
        ->add('save', SubmitType::class, array('label' => 'Enregistrer'))
        ->getForm();

    $dummyForm->handleRequest($request);

    if ($dummyForm->isSubmitted() && $dummyForm->isValid()) {
        $em = $this->getDoctrine()->getManager();
        $em->persist($musee);
        $em->flush();

        return $this->redirectToRoute('musee_show', array('id' => $musee->getId()));
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

<a href="http://symfony.com/doc/current/book/validation.html">Documentation sur la validation.</a>



```php
<?php
// src/AppBundle/Entity/Musee.php
// ...

/**
 * @var string
 *
 * @Assert\NotBlank()
 *
 * @ORM\Column(name="nom", type="string", length=100)
 */
private $nom;  

//...

/**
 * @var string
 *
 * @Assert\Url()
 *
 * @ORM\Column(name="siteWeb", type="string", length=255, nullable=true)
 */
private $siteWeb;

// ...

/**
 * @var string
 *
 *
 * @Assert\Regex(
 *     pattern="/{\d}5/",
 *     message="Le code postal doit être composé de 5 chiffres."
 * )
 *
 * @ORM\Column(name="codePostal", type="string", length=5, nullable=true)
 */
private $codePostal;


```


## Classe dédiée de création de formulaire

Pour plus de clareté et de réutilisabilité du code, on va utiliser des classes dédiées pour la création de formulaires en fonction des entités. On enregistre ces classes dans un dossier dédié (`src/AppBundle/Form/`). On définit en fait un _type_ pour l’entité, utilisable par un formulaire.

Tout comme the type `TexType` est reconnu par la classe Form (avec un `<input type="text">`, etc.) notre entité `Musee` va avoir un type qui lui est propre. avec des _inputs_, des _seletcs_, _etc._


On note :

- que la classe hérite de `AbstractType`
- que 2 méthodes sont nécessaires `buildForm` et `configureOptions`
- que la méthode `configureOptions` donne accès à l'entité `Musee`
- que les appel a `add` sont par défaut mais qu'on peut les modifier en fonction des besoins de validation de bornes de valeurs par défaut, d'affichage/présentation par défaut, etc.


```php
<?php
// src/AppBundle/Form/MuseeType.php

namespace AppBundle\Form;

use Symfony\Component\Form\AbstractType;
use Symfony\Component\Form\FormBuilderInterface;
use Symfony\Component\OptionsResolver\OptionsResolver;

class MuseeType extends AbstractType
{
    /**
     * @param FormBuilderInterface $builder
     * @param array $options
     */
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder
            ->add('nom')
            ->add('adresse')
            ->add('codePostal')
            ->add('ville')
            ->add('siteWeb')
            ->add('coordonnees')
            ->add('status')
            ->add('reouverture')
            ->add('fermetureAnnuelle')
            ->add('periodesOuverture')
        ;
    }

    /**
     * @param OptionsResolver $resolver
     */
    public function configureOptions(OptionsResolver $resolver)
    {
        $resolver->setDefaults(array(
            'data_class' => 'AppBundle\Entity\Musee'
        ));
    }
}
```
Ensuite il suffit de modifier le contrôleur :

```php
<?php
// src/AppBundle/Controller/MuseeController.php

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
     $form = $this->createForm('AppBundle\Form\MuseeType', $musee);
     $form->handleRequest($request);

     // ...
```




Exemple plus réaliste pour le `MuseeType` et pour les templates TWIG:

```php
<?php
// src/AppBundle/Form/MuseeType.php
// ...
use Symfony\Component\Form\Extension\Core\Type\TextType;
use Symfony\Component\Form\Extension\Core\Type\ChoiceType;
use Symfony\Component\Form\Extension\Core\Type\CollectionType;
use Symfony\Component\Form\Extension\Core\Type\UrlType;
use Symfony\Component\Form\Extension\Core\Type\SubmitType;
use Symfony\Component\Form\Extension\Core\Type\TextareaType;
// ...
$builder
    ->add('nom')
    ->add('coordonnees', CollectionType::class)
    ->add('status', ChoiceType::class, array(
        'choices' => array(
            Musee::ouvert => Musee::ouvert ,
            Musee::ferme => Musee::ferme
        )))
    ->add('siteWeb', UrlType::class, ['required' => false])
    ->add('adresse', TextType::class, ['required' => false])
    ->add('codePostal', TextType::class,['required' => false])
    ->add('ville', TextType::class,['required' => false])
    ->add('reouverture', TextType::class,['required' => false])
    ->add('fermetureAnnuelle', TextType::class,['required' => false])
    ->add('periodesOuverture', TextAreaType::class,['required' => false])
    ->add('save', SubmitType::class)

```

{% raw %}
```liquid
{# src/AppBundle/Resources/views/base.html.twig #}
{% extends 'AppBundle:base.html.twig' %}
{% block body %}
    <div class="large-form">
        <h1>Creation d'un Musée</h1>
        <p class="pull-right">
            <a href="{{ path('musee_index') }}"><span class="glyphicon glyphicon-th-list"></span> Retour à la liste</a>
        </p>

        {{ form_start(form) }}
            {{ form_errors(form) }}

            {{ form_row(form.nom) }}
            <div class="row">
                <div class="col-xs-12 col-sm-6">
                    <div class="form-group">
                        {{ form_label(form.coordonnees) }}
                        <div class="row">
                            <div class="col-xs-6">
                                {{ form_errors(form.coordonnees[0]) }}
                                {{ form_widget(form.coordonnees[0]) }}
                            </div>
                            <div class="col-xs-6">
                                {{ form_errors(form.coordonnees[1]) }}
                                {{ form_widget(form.coordonnees[1]) }}
                            </div>
                        </div>
                    </div>
                </div>
                <div class="col-xs-12 col-sm-6">
                    <div class="form-group">
                        {{ form_label(form.status) }}
                        {{ form_widget(form.status) }}

                    </div>
                </div>
            </div>
        {{ form_row(form.siteWeb) }}
        {{ form_row(form.adresse) }}
        <div class="row">
            <div class="col-xs-3">
                {{ form_row(form.codePostal) }}

            </div>
            <div class="col-xs-9">
                {{ form_row(form.ville) }}

            </div>
        </div>

        {{ form_row(form.reouverture) }}
        {{ form_row(form.fermetureAnnuelle) }}
        {{ form_row(form.periodesOuverture) }}

        {{ form_row(form.save, { 'label': 'Créer' }) }}
        {{ form_end(form) }}
    </div>
{% endblock %}
```
{% endraw %}

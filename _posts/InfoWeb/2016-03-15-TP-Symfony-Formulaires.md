---
layout: post
title: TP Symfony - Gestion des formulaires et utilisateurs
categories:  
- InfoWeb
- lab
published: true
author: Yoann Pigné
published: true
---

Ce TP est une mise en application du [cours](http://pigne.org/teaching/infoweb/lecture/Symfony-Forms) présenté en classe. Le but principal est la **création de formulaires** et la **validation** des données saisies par les utilisateurs à l'aide du _framework_ **Symfony**.


On souhaite continuer le travail commencé lors du [TP Précédent](http://pigne.org/teaching/infoweb/lab/TP-Symfony-Modeles) concernant l'application de gestion de musées.


## Gestion des formulaires

En suivant le [cours](http://pigne.org/teaching/infoweb/lecture/Symfony-Forms) créer une classe `MuseeType` dans le dossier `src/appBundle/Form/` qui permet la création de formulaires pour modifier les musées.

Faire de même pour l'entité Commentaire.

Adapter les **contrôleurs**, les **entités**, et les **vues** pour utiliser correctement les formulaires Symfony et pour que tous les champs soient correctement **validés** lors de la saisie des formulaires.


## Gestion d'authentification et de droits d'utilisateurs

Pour notre application  on souhaite les droits suivants :

- les administrateurs :
  - peuvent créer/modifier/supprimer des musées
- les utilisateurs non-administrateurs:
  - peuvent commenter sur les musées
- les visiteurs non-connectés:
  - peuvent consulter les musées et et les commentaires

Les administrateurs peuvent faire ce que les utilisateurs veuvent faire.

Les utilisateurs connectés peuvent faire ce que les visiteurs peuvent faire.


Symfony possède un mécanisme de base pour la gestion d'utilisateurs. A l'aire un _bundle_ supplémentaire, il est très facile de mettre en place des mécanismes classiques de connexion, déconnexion, et enregistrement de nouveaux utilisateurs.

### Le bundle FOSUserBundle

Suivre les 7 étapes de configuration du bundle ici :
[https://symfony.com/doc/master/bundles/FOSUserBundle/index.html](https://symfony.com/doc/master/bundles/FOSUserBundle)

### _Fixtures_ pour les utilisateurs

Pour simplifier la manipulation des utilisateurs on crée une nouvelle classe de _fixture_ qui permet de générer des utilisateurs avec leur mot de passe. Dans l'exemple suivant la classe  `LoadUserData`  dans le fichier `src/AppBundle/DataFixtures/ORM/LoadUserData.php` permet de créer 2 utilisateurs :

| login | mdp | role |
|-------|-----|------|
| admin | admin | ROLE_ADMIN |
| user  | user | ROLE_USER |


```php
<?php
// src/AppBundle/DataFixtures/ORM/LoadUserData.php

namespace AppBundle\DataFixtures\ORM;

use Doctrine\Common\DataFixtures\FixtureInterface;
use Doctrine\Common\Persistence\ObjectManager;
use Symfony\Component\DependencyInjection\ContainerAwareInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;

class LoadUserData implements FixtureInterface, ContainerAwareInterface
{
    private $container = 'Private';


    public function load(ObjectManager $manager)
    {

        // Get our userManager, you must implement `ContainerAwareInterface`
        $userManager = $this->container->get('fos_user.user_manager');

        // Create our user and set details
        $admin = $userManager->createUser();
        $admin->setUsername('admin');
        $admin->setEmail('admin@domain.com');
        $admin->setPlainPassword('admin');
        //$user->setPassword('3NCRYPT3D-V3R51ON');
        $admin->setEnabled(true);
        $admin->setRoles(array('ROLE_ADMIN'));

        // Update the user
        $userManager->updateUser($admin, true);


        // Create our user and set details
        $user = $userManager->createUser();
        $user->setUsername('user');
        $user->setEmail('user@domain.com');
        $user->setPlainPassword('user');
        //$user->setPassword('3NCRYPT3D-V3R51ON');
        $user->setEnabled(true);
        $user->setRoles(array('ROLE_USER'));

        // Update the user
        $userManager->updateUser($user, true);




        $manager->persist($admin);
        $manager->persist($user);
        $manager->flush();

    }

    public function setContainer(ContainerInterface $container = null)
    {
        $this->container = $container;
    }
    /**
     * @return ContainerInterface
     */
    public function getContainer()
    {
        return $this->container;
    }

}
```

On génère les données avec la commande :

```bash
php bin/console doctrine:fixtures:load
```

### Gestion des droits

Les droits se gèrent ensuite en associant des _roles_ à des _routes_  grâce à des expressions rationnelles dans le fichier `security.yml`

```yml
# ...
access_control:
        - { path: ^/login$, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/register, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/resetting, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/musee/[0-9]+/edit$, role: ROLE_ADMIN }
        - { path: ^/musee/[0-9]+/comment$, role: ROLE_USER }
        - ...
```

### Sécurisation des contrôleurs

On utilise la fonction `denyAccessUnlessGranted` dans les actions de contrôleurs pour s'assurer qu'un utilisateur est autorisé a faire cette action

```php
<?php
class SomeController {

  public function someAction($name)
  {
    // Exception levée si l'utilisateur n'est pas administrateur
    $this->denyAccessUnlessGranted('ROLE_ADMIN', null, 'Unable to access this page!');
// ...

```

### Sécurisation des vues

Dans les vues on va choisir ce que l'on veut montrer en fonctions des droits de l'utilisateur :

{% raw %}
```liquid
{% if is_granted('ROLE_ADMIN') %}
    <a href="...">Delete</a>
{% endif %}

{% if is_granted('ROLE_USER') %}
    <h2>Donnez votre avis</h2>
    {{ form_start(comment_form) }}
    {{ form_widget(comment_form) }}
    {{ form_end(comment_form) }}
{% endif %}
```
{% endraw %}
### Surcharger les templates de FOSUserBundle

[Overriding Default FOSUserBundle Templates](http://symfony.com/doc/current/bundles/FOSUserBundle/overriding_templates.html)

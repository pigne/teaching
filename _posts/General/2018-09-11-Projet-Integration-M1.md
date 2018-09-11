---
layout: post
title: TP Forge - tests unitaires
categories:  
- General
- lab
published: true
author: Yoann Pigné
---

Le but de ce TP est de prendre en main l'outil de gestion de version GitLab de l'université (**la forge**), et de se familiariser avec l'utilisation des tests unitaires pour la réalisation de projets de développement mais aussi pour les différents TP durant le Master iWOCS.

Le travail est réalisé en binôme. le but est de travailler en parallèle sur **des postes différents**, le partage du code se faisant grâce à la forge.

## Avant de commencer : configuration de la forge

Il faut avoir un compte à l'université (personnel de l'université ou étudiants inscrits) pour pouvoir créer un compte sur la forge.

#### Etape 1 : première connexion

La **première connection** se fait obligatoirement via le Service Central d'Authentification de l'université (le CAS), en cliquant sur le bouton "CAS Université Le Havre Normandie" depuis la page d'accueil. 

![Page d'accueil de la Forge]({{ site.baseurl }}/images/2018-09-accueil-forge.png){:.figure}

#### Etape 2 : Email valide et vérifié

Une fois connecté il faut donner une **adresse mail valide** (pas forcement celle de l'université). Un mail de confirmation sera envoyé à l'adresse donnée. Aller sur le lien envoyé par mail pour valider la création du compte.

#### Etape 3 : Définir un mot de passe

Une fois l'adresse email validée, aller dans l'onglet [*password*](https://www-apps.univ-lehavre.fr/forge/profile/password/edit) afin de **définir un mot de passe** (différent de celui de l'université).

#### Etape 4 : Configuration de la machine de travail

Dans un terminal on configure le client GIT local avec les commandes de configuration suivantes (remplacer les mots `LOGIN`, `PRENOM`, `NOM` et `EMAIL` par ce qui convient) :

```bash
git config --global credential.https://www-apps.univ-lehavre.fr.username LOGIN
git config --global user.name "PRENOM NOM"
git config --global user.email "EMAIL"
```

Pour ne pas retaper à chaque fois le mot de passe. Sous Linux taper :

```bash
git config --global credential.helper 'cache --timeout 3600'
```

#### Etape 5 : connexion normale

GitLab est maintenant configuré. Plus besoin d'utiliser le "CAS de L'université" pour se connecter à l'avenir. On peut directement entrer le **login** (ou l'adresse email) et le **mot de passe** définit précédemment, sur la page de connexion.

## But et fonctionnement du TP

Le but est la manipulation de la forge et le travail en équipe. Le travail s'effectue par équipe de deux personnes. Chacun doit travailler sur une machine distincte, **on ne travaille pas a deux sur la même machine**.

Les binômes sont désignés par les encadrants.

La section "travail demandé" va décrire les tâches à remplir dans ce projet. Chaque tâche ou groupe de tâche est attribué soit à Alice, soit à Bob. Alice doit faire les tâches qui la concerne uniquement, de même pour Bob.

On essaye de travailler en parallèle quand cela est possible.

## Le projet (*User Story*)

On veut réaliser un simple modèle objet en Java qui permet de gérer un carnet d'adresses.

Un carnet d'adresses est constitué d'entrées qui peuvent représenter des personnes ou des sociétés. Les informations et la présentation sont différentes s'il s'agis d'une personne ou d'une société.

Une méthode utilitaire permet de lire les contacts à partir d'un fichier texte.

On peut faire des recherches sur le carnet et sélectionner des contacts pour les afficher.

L'affichage se fait en choisissant :

- l'ordre lexicographique (croissant ou décroissant),
- le sens d'affichage (nom ou prénom) et
- le mode présentation (abrégé, simple ou complet).

En plus des méthodes indiquées, toutes les classes possèdent des accesseurs pour leurs champs privés. Le modèle objet est le suivant.

![UML Carnet d'adresses]({{ site.baseurl }}/images/UML_CA.svg)

## Travail Demandé

#### 0. Alice et Bob

Alice et Bob configurent leur environnement GIT (`git config ...`) chacun sur leur machine. Cette étape est indispensable pour signer correctement les validations et pouvoir s'authentifier sur  la forge.

D'une manière générale quand quelqu'un reçoit un Merge Request, il essaye de le traiter rapidement. On ne valide un  `merge request` que s'il est de bonne qualité (code bien écrit, tests et couverture acceptables, etc.). On demande des modifications en commentaire si les critères ne sont pas réunis. L'auteur du merge request devra donc refaire des validations et les envoyer dans la branche de merge jusqu'à ce que le merge soit validé.

#### 1. Alice

Alice va créer le projet en utilisant le [projet de base](https://www-apps.univ-lehavre.fr/forge/pigne/projet-standard) présenté en cours.

Dans un terminal :

- créer un nouveau projet GIT
- Récupérer les modifications du projet de base  (`git pull`)

Sur la forge :

- Créer un nouveau projet
- Ajouter Bob comme membre du projet. Dans le menu "⚙️ Settings" (icon de roue crantée), puis *Members* et lui donner les droits *Master*.
- Dans l'onglet "Share with Group" ajouter le groupe `profs-iWOCS` avec les droits *Reporter*

Dans un terminal a nouveau :

- Connecter le projet local avec le projet nouvellement créé sur la forge  (`git add remote origin https://...`)
- Envoyer les modifications sur ce nouveau projet (`push`)

Si Alice souhaite utiliser un IDE c'est son droit. Si elle utilise IntelliJ, il lui suffit de cliquer sur le bouton "open" et de faire pointer le répertoire du projet local créé en ligne de commande.

#### 2. Bob

Bob clone le projet. S'il utilise IntelliJ il clique sur "*check out from version control*".

Il met à jour les informations dans le fichier README avant de  valider et envoyer, directement dans la branche master. Attention il y aun certain nombre de choses à modifier dans le README :

- le titre du projet,
- les URLs des badges,
- les noms et prénom des 2 membres du groupe. Ajouter une colonne "alias" au tableau pour indiquer qui est Alice et qui est Bob,
- la description du projet dans la section *User Story*

Bob doit aussi  :

- Configurer le champ "*Test Coverage parsing*" dans "*Settings*" -> "*CI/CD*" -> "*General pipelines*"  avec la valeur : `\d+.\d+ \% covered`.
- Mettre à jour le nom du projet dans le fichier de configuration `pom.xml` dans la section `<artifactId>`.

#### 3. Alice

- Récupérer les modifications.
- Vérifie que toutes les modifications sont correctes et que Bob n'a rien oublié. Elle modifie le projet si nécessaire, puis sélectionne, valide et envoie.

#### 4. Bob

- Créer et travailler dans une branche `entree`
- Créer le package `entree`
- Créer l'interface `Entree`
- Créer les énumérations `Présentation`, `Sens` et `Genre`
- Sélectionner, valider et envoyer la branche.
- Faire un *Merge Request* et assigner Alice sur la forge.

#### 5. Alice

- Créer et travailler dans une branche `personne`.
- Créer la classe `Personne`.
  - Ne **pas** écrire le corps de la méthode `recherche`.
  - Une personne peut avoir plusieurs prénoms. On les stocke sous forme de tableau.
  - La méthode `toString` prend en paramètre une énumération `Présentation` et `Sens`. Le sens définit si le nom doit être placé avant les prénoms. En fonction de la valeur de `Présentation`,  la chaîne retournée sera différente.
    - La présentation *abrégée* affiche uniquement les initiales du prénom et le nom (e.g. `"H. J. Potter"`)
    - La présentation *simple* affiche le titre abrégé (M. ou Mme), le premier prénom suivit des initiales des autres prénoms, le nom, et entre parenthèses le nom de la société si elle existe. Exemple : `"M. Albus P. W. B. Dumbledore (Ecole de sorcellerie Poudlard)"`
    - La présentation *complète* affiche un maximum d'information sur plusieurs lignes si nécessaire. Par exemple :

    ```
    M. Albus Perceval Wulfric Brian Dumbledore
      - Société : Ecole de sorcellerie Poudlard
      - Fonction: Directeur
    ```
  - tester tout le code généré avec un classe `PersonneTest` et s'assurer d'une couverture complète du code par les tests.
- Sélectionner, valider et envoyer la branche.
- Faire un *Merge Request* et assigner Bob sur la forge.

#### 6.  Bob

- Créer et travailler dans une branche `societe`.
- Valide le Merge Request d'Alice, ou demander des modifications en commentaire si le code est mauvais.
- Créer la classe `Société`.
  - Ne **pas** écrire le corps de la méthode `recherche`.
  - Les paramètres de la méthode `toString` sont ignorés. On affiche toujours de la même façon une société.
  - tester tout le code généré avec un classe `SociétéTest` et s'assurer d'une couverture complète du code par les tests.
- Sélectionner, valider et envoyer la branche.
- Faire un *Merge Request* et assigner Alice.

#### 7.  Alice

- Créer et travailler dans une branche `societe`.
- Dans le package `carnet` créer l'énumération `Ordre` et la classe  Carnet **sans** ses méthodes
- Sélectionner, valider et envoyer la branche.
- Faire un *Merge Request* et assigner Bob sur la forge.

#### 8.  Bob

- Dans une branche  `lecture` créer la méthode `lectureFichier` qui permet de créer un carnet d'adresse avec ses entrées à partir d'un fichier passé en paramètre. Le fichier contient une entrée par ligne et respecte le format suivant :

  ```
  ID;TYPE;CHAMP1;CHAMP2;CHAMP3
  ```

  Si le TYPE est "PERSONNE" alors les autres champs seront :

  ```
  ID;PERSONNE;PRENOMS;NOM;GENRE;ID_CONJOINT;ID_SOCIETE;FONCTION
  ```

  Si le type est "SOCIETE" :

  ```
  ID;SOCIETE;RAISON_SOCIALE
  ```

  Les prénoms multiples sont séparés par des virgules. Les champs facultatifs peuvent être vides.
  Quelques exemples :

  ```
  1;SOCIETE;Ecole de sorcellerie Poudlard
  2;PERSONNE;Albus,Perceval,Wulfric,Brian;Dumbledore;H;;1;Directeur
  3;PERSONNE;Harry,James;Potter;H;4;1;Elève
  4;PERSONNE;Ginny;Weasley;F;3;1;Elève
  ```

- Des tests sont écrits avec un fichier d'exemple dans la partie  `test` du projet, avec la classe `LectureTest`. On vérifie que la lecture d'un fichier (`src/test/resources/carnet.csv`) donne bien le nombre d'entités  attendues, on vérifie une à une les entités créées.
  ```java
  ClassLoader classLoader = getClass().getClassLoader();
  File file = new File(classLoader.getResource("carnet.csv").getFile();
  ```
- Sélectionner, valider et envoyer la branche `lecture`.
- Faire un *Merge Request* et assigner Alice.

#### 9.  Alice

- Dans une nouvelle branche `recherche_selection`, écrire la méthode `ajoutEntrée` de `Carnet`, créer toutes les méthodes de sélection et de recherche. Écrire également le corp des méthodes recherche de `Personne` et `Société`. Pour la classe  `Personne`, la méthode de `recherche` doit retourner vrai si la chaîne de caractère est contenue dans l'un des prénoms ou dans le nom. Pour les société c'est seulement le champs raison sociale qui est recherché.
- Les tests sont écrits, dans la classe `SelectionTest` où l'on s'assure du bon fonctionnement du code écrit. 
- Sélectionner, valider et envoyer la branche.
- Faire un *Merge Request* et assigner Bob.


#### 10. Alice ou Bob

- Dans une nouvelle branche `affichage` écrire le code des méthodes d'affichage.
- Écrire une application principale (`main`) qui propose une manipulation de tout le carnet d'adresse allant de la lecteur à partir d'un fichier, la sélection via recherche de chaîne et l'affichage de la sélection. Tester toutes les combinaisons d'affichage (croissant/décroissant, simple/abrégé/complet, nom_prénoms/prénoms_nom). Oui, il en a douze.
- Sélectionner, valider et envoyer la branche.
- Faire un *Merge Request*...

#### 11. Bob

- Quand tout est fini, créer une [étiquette git](https://git-scm.com/book/fr/v2/Les-bases-de-Git-%C3%89tiquetage) avec le numéro de version `v1.0`.
- S'assurer que l'étiquette est bien présente sur la forge (voir la section [partage d'étiquette](https://git-scm.com/book/fr/v2/Les-bases-de-Git-%C3%89tiquetage#s_sharing_tags)).
---
layout: post
title: Gestion de version pour le travail en équipe
categories:
- General
- lecture
author: Yoann Pigné
tags:  git
published: true
license: CC-BY-NC-SA
---

Les outils de **gestions de version** se sont imposés comme l'outil central d'un l'écosystème des technologies utilisées pour la réalisation de projets de développement en informatique.

Ces outils sont en particulier utiles pour le **travail en équipe**.

Beaucoup de  **plateformes Web** dédiées au développement de projets en équipe ont la gestion de version au centre de leurs applications.  (Github, BitBucket, GitLab, SourceForge).

Différents types d'outils de gestions de version existent. C'est depuis l'apparition des gestionnaires de version distribués que l'écosystème de développement en équipe s'est vraiment développé (Github, BitBucket, GitLab).

Ces plateformes permettent la mise en place de mécanismes d'**intégration continue** pour l'exécution automatique des testes liés au code, et la diffusion de rapport permettant à toute l'équipe de voire l'avancement du projet.

- [Introduction aux systèmes de gestion de version](#introduction-aux-systèmes-de-gestion-de-version)
- [les plateformes de gestion de projet](#les-plateformes-de-gestion-de-projet)
- [Intégration Continue](#intégration-continue)
- [Demo / Live Coding](#demo--live-coding)

## Introduction aux systèmes de gestion de version

une bonne introduction a GIT se trouve dans le livre [**Pro Git book**](http://git-scm.com/book) par  *Scott Chacon* ([CC BY-NC-SA 3.0](http://creativecommons.org/licenses/by-nc-sa/3.0/)). Ce cours s'inspire grandement des chapitres 1 à 3 de ce livre.

### Quelques définitions

**Diff**
: Diff désigne à la fois le nom de la commande et celui du résultat produit. Le *diff* permet de visualiser la différence textuelle, ligne par ligne, entre 2 fichiers. Quand on compare deux versions d'un même fichier, le diff donne la liste des modifications pour passer de la première version à la seconde.

Exemple. On a un fichier `hello_wold.c` :

```c
void main()
{
  printf("Hello World!\n");
}
```

et le fichier `hello_wold2.c`:

```c
#include <stdio.h>

int main()
{
  printf("Hello World!\n");
  return 0;
}
```

Le diff entre  `hello_wold.c` et `hello_wold2.c` donne :

```diff
diff --git a/hello_world.c b/hello_world2.c
index 14049e2..73c3748 100644
--- a/hello_world.c
+++ b/hello_world2.c
@@ -1,6 +1,8 @@
-void main()
+#include <stdio.h>
+
+int main()
 {
     printf("Hello World!\n");
-
+      return 0;
 }

```

**Dépôt**
: Le dépôt (*repository*) représente plus ou moins la totalité de la basse de données que représente un système de gestion de version. Le dépôt contient toutes les versions (tout l'historique) d'un projet.

**Copie de travail**
: La copie de travail (*working copy*, *directory*) représente le système de fichiers actuelle du projet (l'ensembles des fichiers dans le dossier et les sous-dossier du projet).

**Instantané**
: L'instantané (*Snapshot*) est l'état ponctuel des fichiers d'un système de fichiers (projet, dossier, sélection de fichiers, etc.). Il représente son contenu, à un moment donnée.

***Commit***
: Un *commit* (validation), représente un ensemble de changements réalisés sur le dépôt (*repository*) et qui constitue une nouvelle version atomique du dépôt.  

**Branche**
: Un ensemble de validations (*commits*) ayant divergé de la branche d'origine.

**Système de gestion de version**
: Un système de gestion de version est donc constitué d'au moins un dépôt (*repository*) et d'une copie de travail (*directory*). L'ajout de nouvelles versions dans le système se fait grâce à la création de validations (*commits*) qui sont constitué de diffs entre les instantanés d'un *commit* précédent et de la copie de travail.

![Les systèmes de gestion de version]({{ site.baseurl }}/images/version_control.svg)
{:.figure}

### Historique des systèmes de gestions de version

il y a 3 grandes familles systèmes de gestion de version :

- les systèmes locaux
- les systèmes centralisés
- les systèmes décentralisés

#### les Systèmes locaux (*Version Control Systems*)



Copie manuelle de fichiers locaux.

- `+` Facile à utiliser.
- `-` Facile de se tromper.
- `-` **Pas de travail en équipe**.
- **outils**: rcs


![Local Version Control Systems]({{ site.baseurl }}/images/vcs-diag.png){:.figure}

#### Systèmes de gestion de version centralisés

Un serveur central accessible sur le web contient le dépôt. Les utilisateurs ne possèdent que des copies de travail.

- `+` **Travail en équipe**.
- `-` Un point de vulnérabilité unique (si le serveur tombe personne ne peux travailler).
- `-` la plupart des opérations (diff, logs, historique) requière un accès réseau (lent).
- **outils**: CVS, Subversion

![Systèmes de gestion de version centralisés]({{ site.baseurl }}/images/cvcs-diag.png){:.figure}


#### Systèmes de gestion de version distribués

Chaque utilisateur possède **toute** le dépôt (les banches, les **commits**, etc.) en local.

Il n'y a pas de serveur central. N'importe qui (avec une connexion internet entrante) peut devenir un serveur.

- `+` Pas de vulnérabilité unique.
- `+` La plupart des opérations sont en local (diff, historique, logs, manipulation de branches, etc.). Pas de connexion réseau nécessaire (plus rapide)
- `+` un grand nombre de branches peut être géré facilement.
- **tools**:  Git, Mercurial, Bazaar or Darcs

![Systèmes de gestion de version distribués]({{ site.baseurl }}/images/dvcs.svg){:.figure}

### GIT :  3 états /  3 zones / 3 actions

Dans un dépôt GIT un fichier peut avoir **3 états** différents :

- **Modifié** (*modified*): il a des modification locales, il va falloir le sélectionner (*stage*) pour ensuite valider (*commit*) ses modifications.
- **Sélectionné** (*staged*): ses modification ont été sélectionnées (*staged*) pour être validées (*commited*).
- **Validé** (*commited*): il est synchrone avec le dépôt et ne requière pas de validation.

Ces états correspondent à **3 zones** dans un GIT :

- La **copie de travail** (*directory*), c'est le système de fichier local, zone où les fichiers sont modifiés.
- La **zone de sélection** (*staging area*)
- Le **dépôt** où les modifications sont enregistrées sous forme de validations (*commits*).

Le passage entre ses 3 états se fait par **3 actions**:

- **Sélection** (*stage*) qui sélectionne les fichier pour la validation(commande : `git add`).  
- **Validation**  (*commit*) qui crée le *commit* et l'envoie dans le dépôt (commande: `git commit`).
- **Récupération** (*checkout*) qui récupère un instantané (*snapshot*) depuis le dépôt vers la copie de travail (commande : `git checkout`).

### configuration de GIT

une commande pour configurer GIT : `git config`.

```bash
git config --list
```

Trois niveaux de configuration :

- the system level : `--system` option, modifies `/etc/gitconfig`
- the user level : `--global`option, modifies '~/.gitconfig'
- the repository level : no option, modifies `.git/config`


L'authentification est le paramètre de configuration le plus important qu'il ne faut pas négliger.

```bash
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

### Manipulations de bases avec GIT

Elles sont décrites ici en ligne de commande mais sont également disponible dans les IDE dignes de ce nom.

#### Créer un nouveau dépôt

Souvent on dispose déjà d'un projet existant avec du code déjà écrit avant de décider d'en faire le suivit de version.

`git init` initializes a new repository.

```bash
cd myProject
git init
git add "*.java"
git commit -m "Initial commit for myProject"
```

#### Récupérer un dépôt existant

La commande `git clone` fait plusieurs choses  :

- elle télécharge le dépôt à partir d'une url distante (le *remote*)
- elle récupère (*checkout*) la dernière validation (*commit*) dans la copie de travail.
- elle enregistre l'url passée en paramètre comme étant le dépôt distant par défaut.

```bash
git clone https://github.com/pigne/CountDownWebApp.git
```

plusieurs protocoles de transfère :

- `git://`,
- `http(s)://`,
- `user@server:/path.git`.

#### Vérifier l'état des fichiers

la commande la plus utile :  `git status`.

```bash
$ git status
# On branch master
nothing to commit (working directory clean)
```


#### Commencer le suivit d'un fichier

On utilise la commande `git add`.

- Start editing a file... Say `AUTHORS`
- Check the status with `git status`

```bash
$ git status
On branch master
Initial commit
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	AUTHORS
nothing added to commit but untracked files present (use "git add" to track)
```

- Start tracking with `git add`

```bash
$ git add AUTHORS
$ git status
On branch master
Initial commit
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
  new file:   AUTHORS
```

#### Ignorer des fichiers

Certains fichiers n'ont pas leur place dans le dépôt. Pour tant ils existent dans la copie de travail.

- les fichiers compilés (`\*.class`, `\*.o`, Eclipse's `bin` folder),
- les dépendances externes (`\*.jar`, `\*.so`, `node_modules/`)
- les archives de déploiement  (`\*.tar`, `\*.jar`, `\*.war`),
- les logs et sauvegardes (`\*.log`, `\*~`),
- les fichiers de paramètres des IDE / éditeurs (Eclipse's `.settings/` `.classpath` `.project`).

Le fichier `.gitignore` contient des expressions rationnels qui vont filtrer les fichiers a ignorer :

```bash
target/
.projets
.settings/
.classpath
*.class
*~
```

#### Valider les modifications

La commande `git commit` va faire un instantané (*snapshot*) des modification sélectionnées (*staged*) pour créer une validation (un *commit*) et l'insérer das le dépôt.

La validation a toujours besoin d'un message.

- `git commit` sans l'option `-m` va ouvrir l'éditeur de texte.
- `git commit -m "My commit message that details what happens here..."`pour donner un message en ligne.

#### Supprimer un fichier

suppression du dépôt et du système de fichier

```bash
git rm readme.txt
```

Suppression du dépôt mais le fichier reste dans la copie de travail.

```bash
git rm --cached readme.txt
```

Il faudra une validation (*commit*) pour que la suppression soit prise en compte.

#### Déplacer des fichiers

```bash
git mv file_from file_to
```


#### Historique des validations

```bash
git log
git help log
```

Better use graphical tools  to see git history:

- Linux : git-cola, gitg, git gui, qgit, SmartGit
- Mac / Windows : github GUI
- IDEs usually have good GIT support.

#### Se déplacer dans les différents états du fichier

![Les états d'un fichier]({{ site.baseurl }}/images/fileStates.svg){:.figure}

#### Annuler des validations

la commande `git reset` permet de modifier les validations du dépôt.

Annuler le dernier *commit* mais garder les modifications en local.

```bash
git reset HEAD~1
```

![Commandes GIT de validation]({{ site.baseurl }}/images/gitCommitCommands.svg){:.figure}

### Remotes

Pour pouvoir collaborer on a besoin d'un site distant avec lequel échanger les modifications.

On va échanger les *commits*  entre le dépôt local et le/les dépôts distants.

La commande  `git remote` permet de manipuler de dépôts distants (*remotes*)

```bash
$ git clone https://github.com/pigne/CountDownWebApp.git
$ cd CountDownWebApp
$ git remote -v
origin	https://github.com/pigne/CountDownWebApp.git (fetch)
origin	https://github.com/pigne/CountDownWebApp.git (push)
```

Ajouter: `git remote add [shortname] [url]`

#### Récupérer les modifications d'un  *remote*

`git fetch [remote-name]` va télécharger les nouveaux *commits*  du dépôt distant dans le dépôt local.

`git fetch` ne modifie que le dépôt local, pas la copie de travail.

Pour réellement mixer les validation des 2 dépôts on utilise  `git merge`

la commande `git pull` va utiliser `fetch` et `merge`  

#### Envoyer les modifications vers un *remote*

`git push [remote-name] [branch-name]`

Souvent le *remote* par défaut est "`origin`" branche par défaut  est  "`master`"

```bash
git push origin master
```

![Les Commandes d'envoie et de reception]({{ site.baseurl }}/images/gitPushPullCommands.svg)




### Les branches



Le dépôt peut se représenter comme un réseau de validations (*commits*)

Les validations sont identifiées par :

- un code de contrôle unique (*check-sum*)
- un/des liens vers un/des commits parents.

![Commits Linked with their parents]({{ site.baseurl }}/images/commitsNetwork.svg)



#### Les branches sont des pointeurs

Une branche c'est un pointeur sur une validation.

La branche par défaut s'appelle souvent `master`.

La commande `git branch`permet la manipulation des branches.

`HEAD` est un pointeur particulier qui indique la branche actuellement utilisée dans la **copie de travail**.

![Branches and HEAD Point to commits]({{ site.baseurl }}/images/newBranch.svg)




#### Travailler dans les  branches

`git checkout <branch-name>` déplace le pointeur  `HEAD` vers la branche indéquée et récupère in instantané (*snapshot*) du dernier commit de la branche dans la **copie de travail**.

```bach
$ git checkout cli_branch  # moves HEAD
  # modify some_file
$ git add some_file
$ git commit # moves cli_branch & HEAD
```

![Commits in branches]({{ site.baseurl }}/images/commitBranches.svg)

#### Branches divergentes

Faire des validations dans plusieurs branches les fait diverger.

```bash
$ git checkout master  # moves HEAD
  # modify some_file
$ git add some_file
$ git commit # moves master & HEAD
```

![Diverging History]({{ site.baseurl }}/images/diverged.svg)

#### Fusionner Branches

Quand les modifications et ajouts sont terminés dans la branche thématique, on va souhaiter réintégrer la branche principale.

- Pour fusionner la branche B dans la branche A on doit fait appel à la commande `merge` **depuis la branche A**
- La fusion de 2 branches peut :
  - générer un conflit si les modifications sont incompatibles (e.g. modification des mêmes lignes de code),
  - générer une validation (*commit*) automatique si les modifications peuvent se fusionner automatiquement (e.g. modification de lignes différentes qui n'entrent pas en conflit)
  - l'intégration des validations de la branche B dans A **sans** validation de fusion (*merge commit*) si ocune fusion n'est nécessaire (e.g. modification de fichiers différents).

```bash
git checkout master       ## be sure we are in destination branch
git merge cli_branch
git branch -d cli_branch  ## remove cli_branch as it is useless
```

![Diverging History]({{ site.baseurl }}/images/merging.svg)

#### Resolving conflicts

En cas de conflit, la fusion est mise en pause. La validation est annulée. Les fichiers concernés par les conflits sont marqués comme **non fusionnés** (*unmerged*).

```bash
$ git status
index.html: needs merge
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#   unmerged:   index.html
#
```

- On peut utiliser  `git mergetool` pour appeler l'utilitaire de fusion de fichiers (kdiff3,tkdiff,xxdiff,meld,gvimdiff,opendiff,emerge,vimdiff).
- Ou éditer les fichiers non fusionnés à la main

```bash
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
  please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```

- A la fin, on **re-sélectionne** et on **re-valide**  les fichiers. 

#### Branches distantes

Les branches distantes du dépôt local représentent les branches du dépôt distant. Elles ne sont pas modifiables.

- Une branche distante est notée `[remote_name]/[branch_name]` (e.g. `origin/master`)
- `git clone` va synchroniser la branche locale `master` avec la branche distante  `origin/master`
- quand on valide sur `master` sans publier, la branche se trouve en avance (*ahead*) par rapport à la branche distante.
- On peut envoyer n'importe quelle branche locale vers le dépôt distant :

```bash
git push origin cli_branch
```

- On peut créer une branche locale qui suit (*track*) une branche distante existante

```bash
git fetch origin
git checkout -b bogus123 origin/fix_bug_123
```

- dans ce cas un  `git pull` ou `git push` depuis la branche locale  `bogus123` va faire référence à la branche  `fix_bug_123` du dépôt  `origin`.

## les plateformes de gestion de projet

Il en existe plusieurs. Les plus connues : 

**GitHub**
: La plateforme la plus connue. Héberge gratuitement les projets publics. Souscriptions pour projets privés. Possibilité d'obtenir des projets privés gratuitement (programme éducation). Le code est hébergé chez GitHub.

**BitBucket**
: Similaire à GitHub. Programme étudiant (avec l'email universitaire). Le code est hébergé par Atlasian.

**GitLab**
: Similaire aux précédents dans sa version commerciale (*Entreprise Edition*). Une version open source (*Community Edition*) permet l'installation privée d'un serveur.

### La forge de l'université

L'université du Havre utilise la version *Community Edition* de GitLab. Cette instance est accessible à l'adresse : <https://www-apps.univ-lehavre.fr/forge>

Ce serveur est accessible depuis  l'université comme à l'extérieur.

Une fois votre compte créé, il restera accessible même si vous n'êtes plus étudiant à l'université.

Cette instance GitLab hébergée à l'université est nommée **la forge**.

### Création d'un compte sur la forge

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


### Le projets

Le projet est la notion principale dans la forge. 

Un projet n'est pas qu'un simple dépôt GIT, c'est aussi  :

- les membres de ce projets (utilisateur et groupes avec leurs droits)
- les tickets (issues) du projet
- de la documentation libre (Wiki) 
- les  informations sur les exécutions de tests (l'intégration continue)
- les demandes de fusions (*merge requests*)

Tout le monde peut créer des projets. 

Il y a  trois niveaux de visibilité d'un projet : 

- privé : uniquement visible par les membres du projet
- interne  : accessible aux utilisateurs connectés
- public : visible sans authentification

Les droits d'accès en écriture eux sont régis par les droits données aux membres du projets. C'est le propriétaire du projet qui définit les droits qu'ont les membres.

### Les groupes

Tous les utilisateurs ont la possibilité de créer des groupes. Les groupes permettent de gérer plus facilement les droits sur les projets.

### *Fork* de projets

Un projet peut être créé à partir d'un autre projet. C'est une sorte de copie du projet de départ. Lors d'un *fork* Le nouveau projet hérite de l'historique du dépôt du projet de départ. Les membres, les issues et autres éléments du projet sont a définir. 

Pour *forker* un projet on clique le l'icône "fork" du projet que l'on veut copier

![Page de projet sur la Forge]({{ site.baseurl }}/images/2018-09-page-projet-forge.png){:.figure}

### Fusions et demandes de fusions *Merge Requests*

La fusion peut être faite entre deux branches du même projets. les demandes de fusion (*merge requests* ou *pull requests*) ont deux cas d'utilisations possibles : 

- Dans le travail en équipe on utilise les *merge requests*  avant d'intégrer de nouvelles fonctionnalités ou une correction de bug. Cela permet d'obtenir la relecture d'un membre de l'équipe avant que les modifications soient intégrées. 
- Dans le cadre du master les *merge requests* sont un moyen de rendre un TP. Si on imagine qu'un TP et son énoncé sont disponibles sous forme d'un projet sur la forge, alors, faire le TP revient à faire un *fork* du projet, puis à faire effectivement ce qui esr demandé en ajoutant et modifiant les fichiers et enfin à demander un *merge request* auprès du projet de départ pour rendre ce TP.

### Les tickets (*issues*)

Ils permettent premièrement de gérer et corriger les bugs dans une projet. Mais on peut les [utiliser pour faire du développement en méthodes agiles](https://about.gitlab.com/2018/03/05/gitlab-for-agile-software-development/). En particulier :  
- Les [*tasks lists*](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/user/markdown.md#task-lists)
- Les [*issues boards*](https://docs.gitlab.com/ce/user/project/issue_board.html)

## Intégration Continue

Son but est d'automatiser l'intégration et la vérification du code source d'un projet.

En méthodes Agiles On utilise aussi l'intégration continue (CI) avec du deployment continue.

Concrètement faire de l'intégration continue signifie que les tests d'un projet son exécutés à chaque fois qu'une du code est validé dans le gestionnaire de version et le résultat de cette exécution est visible par les membres du projet.

### Tests Unitaires

Pour s'assurer qu'un projet fait ce que l'on veut on doit vérifier qu'il se comporte bien dans tous les cas de figures. C'est une tache difficile, mais en découpant les différentes fonctionnalités d'un projet complexe et en testant chacune d'elles, on réduit les chances d'avoir des bugs.

Les tests les plus simples sont les tests unitaires. chaque fonctionnalité est testée séparément du reste du projet.

Par exemple si je travaille à la réalisation d'une calculatrice, je vais vouloir tester séparément toutes les fonctionnalités. Pour tester la fonctionnalité  "Racine Carré d'un nombre réelle", en imaginant que la fonction `Math.sqrt` n'existe pas, je vais vérifier que :

- `racineCarréRéelle(9);` donne bien 3 ;
- `racineCarréRéelle(0);` donne bien 0 ;
- `racineCarréRéelle(-1);` donne bien `Double.NaN` ;
- `Double d = null; racineCarréRéelle(d);` envoie bien  `NullPointerException`.

Un test unitaire est un ensemble de tests de cas particuliers que j'ai définis et dont je connais le résultat attendu. Le teste me permet de vérifier que la fonction a bien le bon comportement avec ces paramètres la.

Le test doit être le plus pertinent possible. Certes je n'ai pas testé que `racineCarréRéelle(25) == 5` mais j'espère que le teste avec un seul nombre positif suffit.

### Tests Unitaires en java

On utilise la bibliothèque `JUnit`.

```java
class CalculatorTests {

  @Test
  @DisplayName("1 + 1 = 2")
  void addsTwoNumbers() {
    Calculator calculator = new Calculator();
    assertEquals(2, calculator.add(1, 1), "1 + 1 should equal 2");
  }

  @ParameterizedTest(name = "{0} + {1} = {2}")
  @CsvSource({
    "0,    1,   1",
    "1,    2,   3",
    "49,  51, 100",
    "1,  100, 101"
  })
  void add(int first, int second, int expectedResult) {
    Calculator calculator = new Calculator();
    assertEquals(expectedResult, calculator.add(first, second),
      () -> first + " + " + second + " should equal " + expectedResult);
  }
}
```

### Gestion de production Maven

Maven est un outil qui facilite la compilation et la gestion des dépendances pour les projets en java.

Un projet géré avec Maven possède un fichier de configuration `pom.xml` à la racine du projet.

On peut ensuite faire toutes les taches de gestion de dépendance, compilation, test et distribution en ligne de commande ou avec un IDE gérant maven (InteliJ IDEA, Eclipse)

### Intégration Continue dans GitLab

On peut configurer la forge pour l'[Intégration Continue](https://docs.gitlab.com/ce/ci/quick_start/). On configure le projet avec un fichier de configuration (`.gitlab-ci.yml`) à la racine du projet.

Pour se familiariser avec l'intégration continue dans la forge, on peut se référer au [Projet Standard]( https://www-apps.univ-lehavre.fr/forge/pigne/projet-standard.git) qui contient un petit projet avec des tests unitaires, configuré pour que les tests  s'exécutent et que le taux de couverture apparaisse sur les  différentes validations. 

## Demo / Live Coding

Alice et Bob :

- Projet commun sur la forge
- Chacun sa machine
- Alice utilise ligne de commande + éditeur "simple", Bob un IDE

### User Story

    "Donner le temps restant jusqu'à (ou écoulé depuis) une date donnée."

- Notion générale de temps et date  ? Jours, Mois, Années, Heures, Minutes, Secondes.
- Écart de temps par rapport à quoi ? la date actuelle.
- Format de la "date donnée" ? Chaîne de caractères ("`JJ/MM/AAAA HH:MM:SS`")
- Format du "temps restant" ? "`%d jours, %d heures, %d minutes, %d secondes`"

### Alice 1

- Sur la plateforme creation d'un groupe et d'un projet. Ajout de Bob au groupe. Ajout d'un *issue* indiquant la *user story*.
- En local création d'un projet basé sur `projet-standard` modification du README et envoie sur le nouveau projet

#### log

1. Sur la forge :
  - Création d'un groupe (`AB-production`)
  - Ajout de Bob dans le groupe
  - Création d'un projet (`Dates`)
  - Ajout d'une issue "User Story" dans le projet Dates
2. En ligne de commande :
```sh
mkdir Dates && cd Dates
git init
git pull https://www-apps.univ-lehavre.fr/forge/pigne/projet-standard.git
```
3. Avec l'éditeur de texte
  - Modification du fichier README.md
    - Titre du projet
    - urls des badges
    - noms des auteurs
    - Description du projet (*User Story*)
  - Modifier le `groupId` et le `artifactId` dans le fichier `pom.xml`
4. En ligne de commande :
```sh
git add README.md
git commit -m "Mise a jour du fichier README pour le projet Dates avec Alice et Bob"
# connection à la forge
git remote add origin https://alice@www-apps.univ-lehavre.fr/forge/AB-production/Dates.git
# envoie des modifications
git push -u origin master
```

5. Sur la forge: 
   - Création d'un ticket "User Story"

### Bob 1

- Clone le projet avec un IDE
- Implémente une première version simple.
- création et envoie d'une branche.
- création d'un merge request

#### log

un seul fichier `src/main/java/fr/univlehavre/date/Dates.java`:

```java
    static String difference(String theDate) {
        String pattern = "dd/MM/yyyy HH:mm:ss";
        Date d2 = null;
        try {
            d2 = new SimpleDateFormat(pattern).parse(theDate);
        } catch (ParseException e) {
            return "Erreur dans le format de date.";
        }
        Date d1 = new Date();
        long diff = d2.getTime() - d1.getTime();
        long diffSeconds = diff / 1000 % 60;
        long diffMinutes = diff / (60 * 1000) % 60;
        long diffHours = diff / (60 * 60 * 1000) % 24;
        long diffDays = diff / (24 * 60 * 60 * 1000);
        return String.format("%d jours, %d heures, %d minutes, %d secondes", diffDays, diffHours, diffMinutes, diffSeconds);
    }
}
```


### Alice 2 (A2)

- constate le merge request sur la forge et le manque de couverture des tests

### Bob 2

Que fait la méthode `difference` ?

1. conversion du paramètre (chaîne de caractère) en date
2. Création d'une date de référence
3. Le véritable calcul de différence de date
4. construction d'une de la chaîne résultat

Il faut séparer ces 4 actions pour pouvoir les tester. 

```java
public class Dates {
    static String difference(String theDate) {
        // 1. conversion string -> date
        Date d2 = null;
        try {
            d2 = stringToDate(theDate);
        } catch (ParseException e) {
            return "Erreur dans le format de date.";
        }
        
        // 2. date de référence
        Date d1 = new Date();

        // 3. Le véritable calcul de diff de date
        DateDiff dd = differenceDates(d2, d1);

        // 4. construction d'une string résultat
        return diffToString(dd);
    }

    static DateDiff differenceDates(Date d2, Date d1) {
        long diff = d2.getTime() - d1.getTime();
        DateDiff dd = new DateDiff();
        dd.seconds = diff / 1000 % 60;
        dd.minutes = diff / (60 * 1000) % 60;
        dd.hours = diff / (60 * 60 * 1000) % 24;
        dd.days = diff / (24 * 60 * 60 * 1000);
        return dd;
    }

    static Date stringToDate(String theDate) throws ParseException {
        String pattern = "dd/MM/yyyy HH:mm:ss";
        Date d2 = new SimpleDateFormat(pattern).parse(theDate);
        return d2;
    }

    static String diffToString(DateDiff dd) {
        return String.format("%d jours, %d heures, %d minutes, %d secondes", dd.days, dd.hours, dd.minutes, dd.seconds);
    }
}
```


```java
public class DateDiff {
    public long days;
    public long seconds;
    public long minutes;
    public long hours;
    public DateDiff(long d, long h, long m, long s) {
        days = d;
        hours = h;
        minutes = m;
        seconds = s;
    }
    public DateDiff() {
    }
}
```

```java
class DatesTest {
    @Test
    void differenceDates() {
        Date d1 = new Date(0);
        Date d2 = new Date((3662)*1000);
        DateDiff dd = Dates.differenceDates(d2, d1);
        assertEquals(0, dd.days);
        assertEquals(1, dd.hours);
        assertEquals(1, dd.minutes);
        assertEquals(2, dd.seconds);
    }
    @Test
    @DisplayName("bad format should raise exception")
    void stringToDateError() {
        assertThrows(ParseException.class, () -> Dates.stringToDate("nope"),"bad format");
    }
    @Test
    @DisplayName("proper format should pass")
    void stringToDate() {
        try {
            Dates.stringToDate("12/12/1212 12:12:12");
        } catch (ParseException e) {
            fail("should not generate exception");
        }
        assertTrue(true,"No error on proper format");
    }
    @ParameterizedTest(name = "d: {0} h: {1} m{2} s{3} : {4}")
    @CsvSource({
            "1, 2, 3, 4, '1 jours, 2 heures, 3 minutes, 4 secondes'",
            "-4, -3, -2, -1,'-4 jours, -3 heures, -2 minutes, -1 secondes'"
    })
    void diffToString(long d, long h, long m, long s, String expected) {
        DateDiff dd = new DateDiff(d, h, m, s);
        assertEquals(expected, Dates.diffToString(dd));
    }
    @Test
    void differenceError(){
        assertEquals("Erreur dans le format de date.", Dates.difference("nope"));
    }
    @Test
    void differenceOk(){
        String d = Dates.difference("12/12/1212 12:12:12");
        assertNotEquals("Erreur dans le format de date.",d);
        assertEquals(String.class, d.getClass());
    }
}
```

- ajoute les fichier et envoie a nouveau dans le merge request.
  
### Alice

Constate la couverture de tests. Peut *merger* en ligne.


<!-- 
<script>
	var elements = document.querySelectorAll(".figure");
	for (var i = 0 ; i < elements.length; i++){
		var img = elements[i].querySelector('img');
		var caption = document.createElement('p');
		caption.appendChild(document.createTextNode(img.getAttribute('alt')));
		caption.setAttribute('class', 'caption')
		elements[i].appendChild(caption);
	};
</script> -->

<script>
	var elements = document.querySelectorAll(".figure");
	for (var i = 0 ; i < elements.length; i++){
    const alt = elements[i].getAttribute('alt');
    if( alt !== null && typeof alt !== 'undefined' && ! alt.length !== 0 ) {
      const p = elements[i].parentNode;
      const fig = document.createElement('figure');
      p.parentNode.insertBefore(fig, p);
      fig.appendChild(elements[i]);
		  const caption = document.createElement('figcaption');
		  caption.appendChild(document.createTextNode(alt));
      fig.appendChild(caption);
      p.parentNode.removeChild(p);
    }
	};
</script>

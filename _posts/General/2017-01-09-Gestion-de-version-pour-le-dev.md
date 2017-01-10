---
layout: post
title: Gestion de version pour le développement en équipe
categories:
- General
- lecture
author: Yoann Pigné
tags:  git
published: true
license: CC-BY-NC-SA
---


Les outils de **gestions de version** se sont imposés comme l'**outil central** d'un l'écosystème des technologies utilisées pour la réalisation de **projets de développement en informatique**.

Ces outils sont en particulier utiles pour le **travail en équipe**.

Beaucoup de  **plateformes Web** dédiées au développement de projets en équipe ont la gestion de version au centre de leurs applications.  (Github, BitBucket, GitLab, SourceForge).

Différents types d'outils de gestions de version existent. C'est depuis l'apparition de la dernière famille d'outils (les gestionnaires de version distribués) que l'écosystème de développement en équipe s'est vraiment développé (Github, BitBucket, GitLab).

## Introduction au systèmes de gestion de version

une bonne introduction a GIT se trouve dans le livre [**Pro Git book**](http://git-scm.com/book) par  *Scott Chacon* ([CC BY-NC-SA 3.0](http://creativecommons.org/licenses/by-nc-sa/3.0/)). Ce cours s'inspire grandement des chapitres 1 à 3 de ce livre.

### Quelques définitions

Diff
: Diff désigne à la fois le nom de la commande et celui du résultat produit. Le diff permet de visualiser la différence textuelle, ligne par ligne, entre 2 fichiers. Quand on compare deux versions d'un même fichier, le diff donne la liste des modifications pour passer de la première version à la seconde.

Exemple. On a un fichier `hello_wold.c` :

```c
int main()
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
index a39ceeb..73c3748 100644
--- a/hello_world.c
+++ b/hello_world2.c
@@ -1,5 +1,8 @@
+#include <stdio.h>
+
 int main()
 {
     printf("Hello World!\n");
+      return 0;
 }

```

Dépôt
: Le dépôt (*repository*) représente plus ou moins la totalité de la basse de données que représente un système de gestion de version. Le dépôt contient toutes les versions (tout l'historique) d'un projet.

Copie de travail
: La copie de travail (*working copy*, *directory*) représente le système de fichiers actuelle du projet (l'ensembles des fichiers dans le dossier et les sous-dossier du projet).

Instantané
: L'instantané (*Snapshot*) est l'état ponctuel des fichiers d'un système de fichiers (projet, dossier, sélection de fichiers, etc.). Il représente son contenu, à un moment donnée.

*Commit*
: Un *commit* (validation), représente un ensemble de changements réalisés sur le dépôt (*repository*) et qui constitue une nouvelle version atomique du dépôt.  

Branche
: Un ensemble de validations (*commits*) ayant divergé de la branche d'origine.

Système de gestion de version
: Un système de gestion de version est donc constitué d'au moins un dépôt (*repository*) et d'une copie de travail (*directory*). L'ajout de nouvelles versions dans le système se fait grâce à la création de validations (*commits*) qui sont constitué de diffs entre les instantanés d'un *commit* précédent et de la copie de travail.

<p class="text-center figure">
![Les systèmes de gestion de version.]({{ site.baseurl }}/images/version_control.svg)
</p>


## Historique des systèmes de gestions de version

il y a 3 grandes familles systèmes de gestion de version :

- les systèmes locaux
- les systèmes centralisés
- les systèmes décentralisés

### les Systèmes locaux (*Version Control Systems*)



Copie manuelle de fichiers locaux.
- `+` Facile à utiliser.
- `-` Facile de se tromper.
- `-` **Pas de travail en équipe**.
- **outils**: rcs


<p class="text-center figure">
![Local Version Control Systems.]({{ site.baseurl }}/images/vcs-diag.png)
</p>


### Systèmes de gestion de version centralisés

Un serveur central accessible sur le web contient le dépôt. Les utilisateurs ne possèdent que des copies de travail.

- `+` **Travail en équipe**.
- `-` Un point de vulnérabilité unique (si le serveur tombe personne ne peux travailler).
- `-` la plupart des opérations (diff, logs, historique) requière un accès réseau (lent).
- **outils**: CVS, Subversion

<p class="text-center figure">
![Systèmes de gestion de version centralisés.]({{ site.baseurl }}/images/cvcs-diag.png)
</p>



### Systèmes de gestion de version distribués

Chaque utilisateur possède **toute** le dépôt (les banches, les **commits**, etc.) en local.

Il n'y a pas de serveur central. N'importe qui (avec une connexion internet entrante) peut devenir un serveur.
- `+` Pas de vulnérabilité unique.
- `+` La plupart des opérations sont en local (diff, historique, logs, manipulation de branches, etc.). Pas de connexion réseau nécessaire (plus rapide)
- `+` Thousands of branches can be gracefully handled.
- **tools**:  Git, Mercurial, Bazaar or Darcs

<p class="text-center figure">
![Systèmes de gestion de version distribués.]({{ site.baseurl }}/images/dvcs.svg)
</p>


## GIT :  3 états /  3 zones / 3 actions

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


## configuration de git


une commande pour configurer GIT : `git config`.

```bash
git config --list
```

3 niveaux de configuration :
- the system level : `--system` option, modifies `/etc/gitconfig`
- the user level : `--global`option, modifies '~/.gitconfig'
- the repository level : no option, modifies `.git/config`


L'authentification est le paramètre de configuration le plus important qu'il ne faut pas négliger.

```bash
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```


## Manipulations de bases avec GIT


### Créer un nouveau dépôt

Usually you already have a folder with a project in it. Source files and so.

`git init` initializes a new repository.

```bash
cd myProject
git init
git add "*.java"
git commit -m "Initial commit for myProject"
```



###  Récupérer un dépôt existant

La commande `git clone` fait plusieurs choses  :

- elle télécharge le dépôt à partir d'une url distante (le *remote*)
- elle récupère (*checkout*) la dernière validation (*commit*) dans la copie de travail.
- elle enregistre l'url passée en paramètre comme étant le dépôt distant par défaut.


```bash
$ git clone https://github.com/pigne/CountDownWebApp.git
```

plusieurs protocoles de transfère :

- `git://`,
- `http(s)://`,
- `user@server:/path.git`.



### Vérifier l'état des fichiers

la commande la plus utile :  `git status`.

```bash
$ git status
# On branch master
nothing to commit (working directory clean)
```


### Commencer le suivit d'un fichier

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

```
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


### Valider les modifications

La commande `git commit` va faire un instantané (*snapshot*) des modification sélectionnées (*staged*) pour créer une validation (un *commit*) et l'insérer das le dépôt.

La validation a toujours besoin d'un message.

- `git commit` sans l'option `-m` va ouvrir l'éditeur de texte.
- `git commit -m "My commit message that details what happens here..."`pour donner un message en ligne.

### Supprimer un fichier

suppression du dépôt et du système de fichier

```bash
 git rm readme.txt
```

Suppression du dépôt mais le fichier reste dans la copie de travail.

```bash
 git rm --cached readme.txt
```


Il faudra une valdation (*commit*) pour que la suppression soit prise en compte.


### Déplacer des fichiers


```bash
$ git mv file_from file_to
```


### Historique des validations

```bash
$ git log
$ git help log
```

Better use graphical tools  to see git history:

- Linux : git-cola, gitg, git gui, qgit, SmartGit
- Mac / Windows : github GUI
- IDEs usually have good GIT support.




### Se déplacer dans les différents états du fichier

<p class="text-center figure">
![Files States]({{ site.baseurl }}/images/fileStates.svg)
</p>

### Annuler des validations

la commande `git reset`permet de modifier les validations du dépôt.

Annuler le dernier *commit* mais garder les modifications en local.

```bash
$ git reset HEAD~1
```



<p class="text-center figure wide-image noborder-image">
![Git Commit Commands]({{ site.baseurl }}/images/gitCommitCommands.svg)
</p>


## Remotes

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



### Récupérer les modifications d'un  *remote*

`git fetch [remote-name]` va télécharger les nouveaux *commits*  du dépôt distant dans le dépôt local.

`git fetch` ne modifie que le dépôt local, pas la copie de travail.

Pour réellement mixer les validation des 2 dépôts on utilise  `git merge`

la commande `git pull` va utiliser `fetch` et `merge`  


### Envoyer les modifications vers un *remote*

`git push [remote-name] [branch-name]`


Souvent le *remote* par défaut est "`origin`" branche par défaut  est  "`master`"

```bash
$ git push origin master
```


<p class="text-center figure wide-image noborder-image">
![Git Push/Pull Commands]({{ site.baseurl }}/images/gitPushPullCommands.svg)
</p>



### Les branches



Le dépôt peut se représenter comme un réseau de validations (*commits*)

Les validations sont identifiées par :
- un code de contrôle unique (*check-sum*)
- un/des liens vers un/des commits parents.

<p class="text-center  wide-image noborder-image">
![Commits Linked with their parents]({{ site.baseurl }}/images/commitsNetwork.svg)
</p>




### Les branches sont des pointeurs

Une branche c'est un pointeur sur une validation.

La branche par défaut s'appelle souvent `master`.

La commande `git branch`permet la manipulation des branches.

`HEAD` est un pointeur particulier qui indique la branche actuellement utilisée dans la **copie de travail**.

<p class="text-center  wide-image	 noborder-image">
![Branches and HEAD Point to commits]({{ site.baseurl }}/images/newBranch.svg)
</p>




### Travailler dans les  in branches

`git checkout <branch-name>` déplace le pointeur  `HEAD` vers la branche indéquée et récupère in instantané (*snapshot*) du dernier commit de la branche dans la **copie de travail**.

```bach
$ git checkout cli_branch  # moves HEAD
$ # modify some_file
$ git add some_file
$ git commit # moves cli_branch & HEAD
```

<p class="text-center  wide-image	 noborder-image">
![Commits in branches]({{ site.baseurl }}/images/commitBranches.svg)
</p>




### Branches divergentes

Faire des validations dans plusieurs branches les fait diverger.

```bash
$ git checkout master  # moves HEAD
$ # modify some_file
$ git add some_file
$ git commit # moves master & HEAD
```

<p class="text-center  wide-image	 noborder-image">
![Diverging History]({{ site.baseurl }}/images/diverged.svg)
</p>



### Fusionner Branches

As soon as concurrent modifications are finished, we want to reintegrate branches with `git merge`

- To merge branch B in branch A, one have to call `merge` from branch A.
- Merging 2 branches will create a new commit with 2 (or more) parents.
- Conflicts may happen.

```bash
$ git checkout master       ## be sure we are in destination branch
$ git merge cli_branch
$ git branch -d cli_branch  ## remove cli_branch as it is useless
```





<p class="text-center  wide-image	 noborder-image">
![Diverging History]({{ site.baseurl }}/images/merging.svg)
</p>


</section>
<section>

### Resolving conflicts

In case of conflict the commit is aborted and problematic files are unmerged

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

- Use `git mergetool` to call one of the merging tools available (kdiff3,tkdiff,xxdiff,meld,gvimdiff,opendiff,emerge,vimdiff).
- Or edit the unmerged files.

```bash
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
  please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```

- At the end **re-stage** files and **commit**.



### Remote branches

Remote branches are references to the state of branches on your remote repositories. They’re local branches that you can’t move.

- A remote branch is denoted as `[remote_name]/[branch_name]` (e.g. `origin/master`)
- `git clone` will set local `master` according to `origin/master`
- when you commit on `master` but do not "push", then you are "ahead".
- We can push a local branch to a remote:

```bash
$ git push origin cli_branch
```

- We can create a new local branch that "tracks" a remote one:

```bash
$ git fetch origin
$ git checkout -b bogus123 origin/fix_bug_123
```

- any `git pull` or `git push` from the branch `bogus123` will refer to remote  `origin` with branch `fix_bug_123`.





<script>
	var elements = document.querySelectorAll(".figure");
	for (var i = 0 ; i < elements.length; i++){
		var img = elements[i].querySelector('img');
		var caption = document.createElement('p');
		caption.appendChild(document.createTextNode(img.getAttribute('alt')));
		caption.setAttribute('class', 'caption')
		elements[i].appendChild(caption);
	};
</script>

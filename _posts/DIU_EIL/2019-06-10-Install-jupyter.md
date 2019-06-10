---
layout: post
title: Configurations et installations préliminaires au cours
author: Yoann Pigné
published: true
categories:  
- DIU_EIL
- lecture
---

# Configurations et installations préliminaires au cours

Dans le cadre des cours de de DIU EIL à l'université le Havre Normandie,certaines commandes et configurations  propres à l'environnement de travail sont nécessaires. Les postes disposent de deux systèmes d'exploitation : Windows et Ubuntu (Linux). Python est installé sur les deux systèmes mais des configurations et des installations complémentaires sot nécessaires.

## Accès

Les participants disposent théoriquement d'un compte utilisable à la fois pour se connecter aux postes de travail et aussi pour accéder aux ressources pédagogiques (<https://eureka.univ-lehavre.fr>).

Dans Eureka il faut rechercher le cours `"DIU EIL 1"` et s'y inscrire. C'est ici que se trouvent les ressources et les supports de cours. 

## Connexion

Pour ce cours, on utilisera Linux (Ubuntu). Il faut redémarrer les postes si nécessaire et choisir `"Ubuntu"`  dans la liste, au démarrage du poste.


## Installation de `pip` et configuration du système

A n'exécuter qu'une seule fois sur les postes de l'université :

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3 get-pip.py --user
echo "export PATH=\$PATH:\${HOME}/.local/bin" >> ~/.profile
export PATH=$PATH:${HOME}/.local/bin
```

## Installation de `jupyter`

Les *Jupyter Notebooks* permettent de partager
facilement du code python des équations du texte et des graphiques. 

A n'exécuter qu'une seule fois :

```bash
pip3 install --user jupyter
```

<!-- > /dev/null 2>&1 && echo "OK." || exit_on_error "Erreur." -->

## Utilisation des *notebooks* 

Une fois `jupyter`  installé on peut démarer l'application à partir d'un terminal, dans un dossier ou se trouverons les fichiers *notesbooks*. 

On lance le serveur avec la commande : 

```bash
jupyter notebook
```

Cette commande bloque le terminal et ouvre le navigateur Web sur l'application Web Jupyter.

On peut ensuite suivre le cours, exécuter les exemple et faire les exercices en téléchargent les fichiers *notebooks* et en les lançant dans l'application Web Jupyter.

Remarque : ce document est également un [notebook à télécharger](https://pigne.org/teaching/images/2019-06-10-Install-jupyter.ipynb) et a exécuter : 

```python
1 + 1
```




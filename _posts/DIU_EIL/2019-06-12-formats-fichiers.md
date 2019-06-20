---
layout: post
title: Les formats de fichiers usuels 
author: Yoann Pigné
published: true
categories:  
- DIU_EIL
- lecture
---

# Les formats de fichiers usuels

## Réfrences

- [CSV](https://fr.wikipedia.org/wiki/Comma-separated_values), Wikipédia, le 12 juin 2019
- [Documentation de Pandas](https://pandas.pydata.org/pandas-docs/stable/reference/frame.html), le 12 juin 2019

## archives

## formats de fichiers d'échange de données

### Les formats de données tabulées

Les tableurs proposent de stoker d'information sous forme de tableau. Les données sont référencés par des numéros de lignes et de colonnes. Ces formats sont normalisés (Office Open XML, OpenDocument) et supportés par des logiciels libres (LibreOffice) et propriétaires (Excel, Number).

CSV (*Comma-Separated-Values*) est un format textuel non normalisé (mais faisant l'objet d'une  [RFC](https://tools.ietf.org/html/rfc4180)). Il permet de stocker les données tabulées en identifiant nominativement les colonnes. 

```csv
Civilité,Prénom,Année de naissance
M,Alphonse,1932
F,Béatrice,1964
F,Charlotte,1988
```
En python on manipule des données tabulées facilement avec des objets `DataFrame` de bibliothèque `pandas`. La documentation sur les [dataframes](https://pandas.pydata.org/pandas-docs/stable/reference/frame.html) nos servira par la suite. 

```python
import pandas

data = {"Civilité":("M","F", "F"),'Prénom':('Alphonse','Béatrice', 'Charlotte'), "année":(1932, 1964, 1988)}

df = pandas.DataFrame(data)
print(df.query('Civilité == "F"'))

print("année moyenne: {:.0f}".format(df['année'].mean()))
```

Lecture d'un tableur dans un DataFrame : 

```python
df = pandas.read_excel("https://pigne.org/teaching/DIU_EIL/resultats-2016.xlsx")
print("Dimensions du DataFrame :", df.shape)
df.head()
```

### Exercice 1

Télécharger [ce tableur](https://pigne.org/teaching/DIU_EIL/resultats-2016.xlsx). Suivre le [TP n°1 sur les tableurs de Licence 1](https://pigne.org/teaching/DIU_EIL/seance01-PIX-tableur.pdf) à partir de la question N°6 sans s'attarder sur la mise en forme.

### Exercice 2

- Dans un **nouveau** *notebook*, répondre aux questions 6 à 12, posées dans l'exercice 1 à l'aide de python et des `DataFrame` de `pandas`.
- Envoyer ce notebook par mail à `yoann.pigne@univ-lehavre.fr` avec l'entête "`[DIU-EIL] Exercice DataFrame`" et en indiquant vos nom et prénom dans le corps du mail.


### Données structurées et typés

L'échange de données entre différentes entités est souvent assurés par le format XML. Ce format constitué de balise permet de spécifier le rôle et l'imbrication des balises pour un fichier donné. Les données sont donc typées et structurées. 

Dans les technologies web, le format JSON s'est imposé. Moins expressif que XML il est plus léger et semble plus simle à manipuler. Il est aussi très proche du langage JavaScript.

```python
import json

with open('data.json') as f:
    d = json.load(f)
    print(d)
```

### Exercice 3

Télécharger le [fichier de données JSON](https://pigne.org/teaching/DIU_EIL/data.json). Celui-ci contient un tableau de 3 objets qui ont un champ "data" contenant a son tour un champ "values" ou "value". 

Dans un **nouveau** *notebook*, changer le fichier, puis faire la moyenne des valeurs dans les champs "values" respectivement et afficher de cette façon :

```text
- Température Bureau : (TEMPERATURE = 23.11)
- Porte du Garage : (DOOR = 0)
- Ventilateur Ordinateur Bureau : (FAN_SPEED = 1774.50)
```

Envoyer ce notebook par mail à `yoann.pigne@univ-lehavre.fr` avec l'entête "`[DIU-EIL] Exercice JSON`" et en indiquant vos nom et prénom dans le corps du mail.

## Fichiers images

- les [images matricielles](https://pigne.org/teaching/DIU_EIL/images-matricielles.pdf)
- les images vectorielles

### Exercice 4

Télécharger [l'archive "Images"](https://pigne.org/teaching/DIU_EIL/Images.zip) Suivre le [TP  sur les images matricielles de Licence 1](https://pigne.org/teaching/DIU_EIL/ExerciceImagesMatricielles.pdf). 
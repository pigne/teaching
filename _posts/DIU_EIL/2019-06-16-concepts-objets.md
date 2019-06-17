---
layout: post
title: Les concepts objets
author: Yoann Pigné
published: fasle
categories:  
- DIU_EIL
- lecture
---


# Les concepts objets

## Références

-[Python - Object Oriented](https://www.tutorialspoint.com/python/python_classes_objects.htm), consulté le 12/06/2019.
-[Python OOP](https://www.programiz.com/python-programming/object-oriented-programming), consulté le 12/06/2019.

## Terminologie

### Classe

C'est la définition (le patron) d'un nouveau type de données composite (constitué de plusieurs autre types de données)  qui permet de définir de nouveaux objets. 

### Objet 

c'est un assemblage unique constitué de données dont le type est définie par une classe. 

### instance 

C'est la propriété pour un objet d'être d'une classe donnée. On dit qu'unn objet `o` créé à partir de la classe `A` est une instance de `A`

## Méthode

C'est une cas particulier de fonction définie à l'intérieur d'une classe. Une méthode ne s'exécute (s'applique) que dans le context d'une instance de classe donnée.

### Variable d'instance

C'est une variable définie dans une méthode de classe et qui n'appartient qu'a l'instance de classe courante qui exécute cette méthode. 

### Variable de classe

C'est une variable partagée par toutes les instances de la classe. Elles sont définies dans la classe, en dehors de méthodes. Les variables de classe sont beaucoup moins utilisées que les variables d'instance. 

### Donnée membre 

C'est une variable d'instance ou de classe associée a une classe et ses instances. 

### Surcharge de fonction

C'est l'attribution de plusieurs comportements pour une fonction donnée. Elle se traduit par des variations dans les types d'objets ou d'arguments utilisés dans ces fonctions.


### Surcharge d'opérateur 

C'est l'attribution de plusieurs fonction à un seul opérateur. 


## Déclaration de classes

En python on déclare des classes avec le mot clé `class`, suivit du nom de la classe. Cette déclaration est un bloc qui contient toutes les définitions de la classe.

On commence par déclarer une chaîne de caractère qui documente la classe, puis on définie la méthode `__init__()` qui est le constructeur de la classe. 

```python
class Point:
   'Documentation pour cette classe'
   nbPoints = 0 # déclaration d'une variable de classe

   def __init__(self, x, y):
        print('[constructeur de Point]')
        self.x = x # déclaration d'une variable d'instance
        self.y = y # déclaration d'une variable d'instance
        Point.nbPoints += 1 # accès a une variable de classe

   def afficheNbPoints(self):
        print(f'{Point.nbPoints:d} points au total')
        # ou
        # print("{.nbPoints:d} points au total".format(Point))

   def affichePoint(self):
        print(f'({self.x:.2f}, {self.y:.2f})')
        # ou 
        # print("({0.x:.2f}, {0.y:.2f})".format(self))
```

## Création d'objets

```python
p1 = Point(0, 5)
p2 = Point(10, 5)
p3 = Point(10, 10)
```

## Accès aux attributs

```python
p1.affichePoint()
p2.affichePoint()

p1.afficheNbPoints()
print(Point.nbPoints, "points...")
```

## Héritage

Une classe peut hériter les propriétés d'une autre classe. 

```python
class Point3d(Point): # définition d'une classe fille de Point
   def __init__(self, x, y, z):
      print('[constructeur de Point3d]')
      super().__init__(x, y) # appel au constructeur de Point
      self.z = z 

p3d =  Point3d(1,0,-1)
p3d.affichePoint()
```

## Surcharge de méthode

```python
class Point3d(Point): # définition d'une classe fille de Point
    def __init__(self, x, y, z):
        print('[constructeur de Point3d]')
        super().__init__(x, y) # appel au constructeur de Point
        self.z = z 

    def affichePoint(self):
        print(f"({self.x:.2f}, {self.y:.2f}, {self.z:.2f})")
        # ou 
        # print("({0.x:.2f}, {0.y:.2f}), {0.z:.2f})".format(self))

p3d =  Point3d(1,0,-1)
p3d.affichePoint()
```


## surcharge des méthodes de base

### `__init__ ( self [,args...] )`

Le constructeur, appellée à chaque création d'objets.


### `__del__( self )`

Le destructeur, appellé avant la destruction de chaque objet.

### `__repr__( self )`

Chaîne de caractère permettent de ré-instancier l'objet avec la fonction `eval`:

```python 
class Point3d(Point): 
    # ...
    def __repr__(self):
        return "Point({},{},{})".format(self.x, self.y, self.z)
    # ...
```

La plupart du temps on souhaite que cette expression soit vrai : 
```python 
eval(repr(p3d)) == p3d
```


### `__str__( self )`

Représentation "lisible" de l'objet. Dans notre exemple `Point3d.affichePoint()` joue le role de `__str__()`

```python 
class Point3d(Point):
    # ...
    def affichePoint(self):
        print(f"({self.x:.2f}, {self.y:.2f}, {self.z:.2f})")
    # ...
```

### `__cmp__ ( self, x )`

Comparaison de cet objet avec un autre (du même type). Utile pour l'utilisation d'instance de classes dans des collections ou dans les algorithmes de tri.

## Surcharge d'opérateurs

| Operator | Expression | Internally |
| - | - | - |
| Addition | `p1 + p2` | `p1.__add__(p2)` |
| Subtraction | `p1 - p2` | `p1.__sub__(p2)` |
| Multiplication | `p1 * p2` | `p1.__mul__(p2)` |
| Power | `p1 ** p2` | `p1.__pow__(p2)` |
| Division | `p1 / p2` | `p1.__truediv__(p2`) |
| Floor Division | `p1 // p2` | `p1.__floordiv__(p)` |
| Remainder (modulo) | `p1 % p2` | `p1.__mod__(p2)` |
| Bitwise Left Shift | `p1 << p2` | `p1.__lshift__(p2)` |
| Bitwise Right Shift | `p1 >> p2` | `p1.__rshift__(p2)` |
| Bitwise AND | `p1 & p2` | `p1.__and__(p2)` |
| Bitwise OR | `p1 | p2` | `p1.__or__(p2)` |
| Bitwise XOR | `p1 ^ p2` | `p1.__xor__(p2)` |
| Bitwise NOT | `~p1` | `p1.__invert__()` |


## Surcharge d'opérateurs de comparaison

| Operator | Expression | Internally |
| - | - | - |
| Less than | `p1 < p2` | `p1.__lt__(p2)` |
| Less than or equal to | `p1 <= p2` | `p1.__le__(p2)` |
| Equal to | `p1 == p2` | `p1.__eq__(p2)` |
| Not equal to | `p1 != p2` | `p1.__ne__(p2)` |
| Greater than | `p1 > p2` | `p1.__gt__(p2)` |
| Greater than or equal to | `p1 >= p2` | `p1.__ge__(p2)` |


### Exercice 1

[Quiz sur les Classes](https://www.programiz.com/python-programming/quiz/object-class/take)

### Exercice 2

Dans un notebook ou dans un simple fichier `.py` : 

- Écrire une classe `VecteurND` générique qui peut représenter un vecteur dans un espace a *n* dimensions, *n* étant donné en paramètre du constructeur.
- Écrire les classes `Vecteur2D`et `Vecteur3D` qui héritent de `VecteurND` et ne sont pas paramétriques.
- Surcharger les bons opérateurs et les bonnes méthodes pour que les tests suivants fonctionnent (on veillera à ne pas être redondant)
- Envoyer ce notebook (ou fichier) par mail à `yoann.pigne@univ-lehavre.fr`  avec l'entête `[DIU-EIL] Exercice Classes` et en indiquant vos nom et prénom dans le corps du mail.

```python
v = VecteurND(4)
v.set(0, 10)
v.set(1, 20)
v.set(2, 30)
v.set(3, 40)

catchExpectedError = False
try:
    v.set(4, 50)
except ValueError:
    catchExpectedError = True
    
assert(catchExpectedError)
assert( str(v) == "(10, 20, 30, 40)" )
assert(eval(repr(v)) == v)

v2D1 = Vecteur2D(1,3)
assert(eval(repr(v2D1)) == v2D1)
assert( str(v2D1) == "(1, 3)" )

v2D2 = Vecteur2D(0,4)
assert( str(v2D1 - v2D2) == "(1, -1)" )


v3D1 = Vecteur3D(1,3,5)
assert(eval(repr(v3D1)) == v3D1)
assert( str(v3D1) == "(1, 3, 5)" )


```


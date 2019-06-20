---
layout: post
title: Les concepts objets
author: Yoann Pigné
published: true
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

C'est la définition (le patron) d'un nouveau type de donnée composite. Ces types sont un assemblage  d'autres types de données,   il permettent d'**encapsuler** différentes données (de différents types) dans  de nouveaux objets.

### Objet

c'est un assemblage unique constitué de données dont le type est définie par une classe. 

### Instance

C'est la propriété pour un objet d'être d'une classe donnée. On dit qu'nn objet `o` créé à partir de la classe `A` est une instance de `A`

## Méthode

C'est une cas particulier de fonction définie à l'intérieur d'une classe. Une méthode ne s'exécute (s'applique) que dans le context d'une instance de classe donnée.

### Variable d'instance

C'est une variable définie dans une méthode de classe et qui n'appartient qu'a l'instance de classe courante qui exécute cette méthode. 

### Variable de classe

C'est une variable partagée par toutes les instances de la classe. Elles sont définies dans la classe, en dehors de méthodes. Les variables de classe sont beaucoup moins utilisées que les variables d'instance. 

### Donnée membre 

C'est une variable d'instance ou de classe associée à une classe et ses instances. 

### Surcharge de méthode

C'est l'attribution de plusieurs comportements pour une fonction (méthode) donnée. Elle se traduit par des variations dans les types d'objets ou d'arguments utilisés dans ces fonctions.

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
        return "Point3d({},{},{})".format(self.x, self.y, self.z)
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
| Division | `p1 / p2` | `p1.__truediv__(p2)` |
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


### Exercice 

[Quiz sur les Classes](https://www.programiz.com/python-programming/quiz/object-class/take)



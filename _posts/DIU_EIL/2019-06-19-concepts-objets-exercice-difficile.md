---
layout: post
title: Concepts Objet -- Exercice Complet
author: Yoann Pigné
published: true
categories:  
- DIU_EIL
- lab
---


- Écrire une classe `VecteurND` générique qui peut représenter un vecteur dans un espace a *n* dimensions, *n* étant donné en paramètre du constructeur.
- Écrire les classes `Vecteur2D`et `Vecteur3D` pour lesquelles on n'a pas besoin de spécifier la dimension. On utilisera l'**héritage** pour ces classes. 
- Surcharger les bons opérateurs et les bonnes méthodes pour que les tests suivants fonctionnent (on veillera à ne pas être redondant)


```python
v = VecteurND(4)
v[0] = 10
v[1] = 20
v[2] = 30
v[3] = 40

catchExpectedError = False
try:
    v[4] = 50
except ValueError:
    catchExpectedError = True
    
assert(catchExpectedError)
print("__str__():", str(v))
assert( str(v) == "(10, 20, 30, 40)" )
assert(eval(repr(v)) == v)

## 2D
v2D1 = Vecteur2D(1,3)
print("__repr__()", repr(v2D1))
assert(eval(repr(v2D1)) == v2D1)
assert( str(v2D1) == "(1, 3)" )

v2D2 = Vecteur2D(0,4)
print("__sub__(): ", str(v2D1 - v2D2))
assert( str(v2D1 - v2D2) == "(1, -1)")

print("__mul__(): ", str(v2D1 *2))
assert( str(v2D1 * 2) == "(2, 6)")

print("v2D1.x=", v2D1.x, "v2D1.y=",v2D1.y)
assert( v2D1.x == 1)
assert( v2D1.y == 3)


## 3D
v3D1 = Vecteur3D(1,2,3)

print("__str__():", str(v3D1))
assert( str(v3D1) == "(1, 2, 3)" )

print("__repr__():", repr(v3D1))
assert(eval(repr(v3D1)) == v3D1)

print("__sub__():", str(v3D1 - v3D1))
assert( str(v3D1 - v3D1) == "(0, 0, 0)")

print("__add__():", str(v3D1 + v3D1))
assert( str(v3D1 + v3D1) == "(2, 4, 6)" )

print("__mul__():", str(v3D1 *2))
assert( str(v3D1 * 2) == "(2, 4, 6)")

print("v3D1.x=", v3D1.x, "v3D1.y=",v3D1.y, "v3D1.z=",v3D1.z )
assert( v3D1.x == 1)
assert( v3D1.y == 2)
assert( v3D1.z == 3)

## tester les exceptions sur des types différents
catchExpectedError = False
try:
    str(v3D1 + v2D1)
except ValueError:
    catchExpectedError = True
assert(catchExpectedError)
```


---
layout: post
title: Codage des nombres
author: Yoann Pigné
published: true
categories:  
- DIU_EIL
- lecture
---



# Codage des nombres

## Références

Ce cours s'inspire ***très largement*** de, ou utilise les supports suivants :

-  *[Unités de mesure](https://pigne.org/teaching/images/unites.pdf)* par Jean-Luc Ponty, Université Le Havre Normandie
- *[Codage des données](https://moodle3.unistra.fr/mod/resource/view.php?id=406075)* par Cédric Bastoul, Université de Strasbourg
- *[Two's complement](https://en.wikipedia.org/wiki/Two%27s_complement)* Wikipedia (accédé le 10 juin 2019)

## Nombres Entier

## Unités de mesure

Présentation des [unités de mesure](https://pigne.org/teaching/DIU_EIL/unites.pdf).

### Les entiers non signés

On code les entiers naturels, positifs ou nuls, avec des entier non signés en binaire (base 2) de façon similaire à la base décimale (base 10)


| décimal | binaire |
|-|-|
| 0 |	0 |
| 1 |	1 |
| 2 |	10 |
| 3 |	11 |
| 4 |	100 |
| 5 |	101 |
| 6 |	110 |
| 7 |	111 |
| 8 |	1000 |
| 9 |	1001 |


Avec n bits, on peut coder les entiers naturels de 0 à 2n − 1



### Opérations

#### Conversion de base 2 à base 10

La position des bits donnes la valeur de la puissance de 2. 

```text
110 (2) = 1*2^2 + 1*2^1 + 0*2^0 
        =   4   +   2   +   0  (10)
        =   6 (10)
```

Sommes des puissances de 2 aux positions des "1". 


#### Conversion de base 10 à base 2

On divise le nombre en base 10 par 2 puis successivement les quotients jusqu'à ce qu'il soit nul. Puis on aligne les restes dans le sens inverse  


``` text
6 ┃ 2
0 ┣━━
  ┃ 3 ┃ 2
    1 ┣━━
      ┃ 1 ┃ 2
        1 ┣━━
          ┃ 0

        = 110 (2)
```


#### Additions, soustractions, multiplications, divisions

Identique à la base 10 avec restes et retenus

```text
 r 10111
    10111 (2) = 23 (10)
 +  10011 (2) = 19 (10)
   ------
   101010 (2) = 42 (10)
```

### Les entiers signés (méthode naïve)

On peut être tenté d'utiliser d'utiliser le bit de poids fort pour représenter le signe : 

Exemple sur un octet :

```text
0010 1010 (2) = 42 (10)
1010 1010 (2) ? -42 (10)
```

Problème, l'arithmétique classique ne fonctionne plus :

```text
  1010 1010 (2) ? -42 (10)
+ 0000 0010 (2) = 2 (10)
 -------------------------
  1010 1100 (2) ? -44 (10)
```

### codage en complément a 2

Pour N bits disponibles, on veut coder des nombres entiers prenant des valeurs entre −2N − 1 et 2N − 1 − 1.

On souhaite aussi que les opération arithmétiques fonctionnent. 

On conserve l'idée du signé donné par le bit de poids fort.

Les nombres positifs sont représentés comme d'ordinaire. Les nombres négatif sont représentés en complément a 2. L'opération se fait en 2 étapes : 

1. On prend la représentation binaire du nombre en valeur absolue;
2. on inverse bit  les bits de ce nombre;
3. On ajoute 1 au résultat précédent (les dépassements sont ignorés)

Pour coder (−4) :

1. on prend le nombre positif 4 : `00000100` ;
1. on inverse les bits : `11111011` ;
1. on ajoute 1 : `11111100`.

### opérations

L'addition fonctionne comme d'ordinaire.

```text
   11111100 (2) = -4 (10)
+  00000100 (2) =  4 (10)
-----------
  x00000000 (2) =  0 (10)
```

Pour la soustraction on fait d'abord le complement a 2 de la seconde opérande puis en fait l'addition.

La multiplication et la division peuvent à minima être  semblable a l'algo naïf (additions, resp. soustractions successives) sauf qu'on doit doubler le nombre de bits sur lesquels sont codé les nombres. on doit aussi propager les bit de signe. Par exemple `1010` sur 4 bits devient `1111 1010`. `101` sur 4 bits reste `101` car le bit de poids fort est un `0`.


Simulation de la multiplication avec compléments à 2 :

```python
def c2(n, bits=8):
    return bin(n % (1<<bits))

def printr(str, padding='20'):
    print(("{:>"+padding+"}").format(str))

def multiply(a,b, bits=8):
    shift = 0
    res = 0
    while shift < bits:
        x = (((b >> shift) & 1) * (a<<shift)) % (1<<bits)
        printr(c2(x, 2*bits))
        res += x
        shift+=1
    printr('--------')
    printr(c2(res, 2*bits))
    printr(str(bits)+' bits: '+c2(res, bits))

    res = (res % (1<<bits) )  + ( -1 ^ ((1<<bits)-1) )  * ((res>>(bits-1)) & 1)
    printr(res)
    return res

    
bits = 8
a = 7
print("a =",c2(a))
b = -3
print("b =",c2(b))
print("expected a*b =",a*b)

multiply(a,b,bits)

```

Exercice : En vous inspirant de la [division des nombres entiers par soustractions successives](https://en.wikipedia.org/wiki/Division_algorithm#Division_by_repeated_subtraction) proposer une implémentation en python. Vérifiez que les cela fonctionne quelque soit le signe des opérandes. Proposez in affichage clair.

## Codage des nombres flottants

Mode de représentation des réels. Contrairement aux entiers, cette représentation n'est pas exacte. Elle souffre d'erreurs d'approximations qui se propagent avec les opérations arithmétiques.

La fonction [finfo](https://docs.scipy.org/doc/numpy/reference/generated/numpy.finfo.html) du package `numpy` nous indique les particularités des type de nombres flottants disponibles dans le langage Python.


Pour les flottants classiques de python : 

```python
import numpy as np
print(np.finfo(float))
```

### La norme

La norme d'encodage des nombre flottants est [IEEE_754](https://en.wikipedia.org/wiki/IEEE_754). Il en existe plusieurs versions (plusieurs précisions), mais les plus  utilisées dans les langages modernes actuellement sont [binary64](https://en.wikipedia.org/wiki/Double-precision_floating-point_format) (les *doubles*)

Le codage a la forme  d’une notation scientifique avec :

- un signe, codé sur un bit 
- une mantisse, codé sur 52 bits
- un exposant, codé sur 11 bits, comme un entier signé (complément a 2 ) ou non signé.


(-1)^signe x 2^{exposant -1023} x 1.mantisse

exemple : codage de `-12,125`

- le signe du nombre donne la valeur du bit de signe : `1`
- codage de  la partie entière (positive) en binaire : `12` -> `1100`
- codage de la partie décimal en binaire par multiplications par 2 successives :
    - `0.125 x 2 = **0**.250`
    - `0.25  x 2 = **0**.5`
    - `0.5   x 2 = **1**`
    -  => `0.001`
- assemblage de la partie entière avec la partie décimale puis normalisation
    - `12.125` -> `1100.001` -> `1.100001 x 2^3`
    - l'exposant vaut `3` + `1023` en binaire sur 11 bits : `1026 (10) = 100 0000 0010 (2)`
    - la mantisse est la partie à droite de la virgule complétée par des zéros à droite pour remplir les 52 bits :
        10000100000...000 (2)
- -12.125 (10) = 1 10000000010 1000010000000000000000000000000000000000000000000000 (2)

### Opérations sur les réels

Addition et soustraction sont réalisés en passant les 2 nombres a opérer dans le même exposant avant d'additionner/soustraire normalement les mantisses. Puis re-normaliser si nécessaire. 

Pour Multiplier on additionne les exposant et on multiplie les mantisses, puis le résultat est arrondit et normalisé.

Pour diviser on soustrait l'exposant du diviseur à celui du dividende et en divisant la mantisse du dividende par la mantisse du diviseur.


### Problèmes de réprésentation

#### Problème d'échelle

Du fait du codage par mantisse et exposant, pour un exposant donné on a accès à un ensemble de nombre possibles via la mantisse. Deux exposants différents ne donnent pas accès aux même  nombres. On ne peut faire d'opérations entre deux nombres *trop différents*.  

Trop d'écart avec des petit nombres : 

```python
a= 1.0
b = 0.0000000000000000000000001
print ("a =",a)
print ("b =",b)
print("a + b =", a+b)
```

Trop d'écart avec des grands nombres :

```python 
a= 1.0
b = 10000000000000000000000000.0
print ("a =",a)
print ("b =",b)
print("a + b =", a+b)
```



#### Problème de représentation des décimaux

Les décimaux ont une représentation exacte en base 10 (d'où leur nom) mais elle ne l'est pas forcement en base 2. Pour un décimal simple (*i.e.* `0.1`)  Il peut exister un grand nombre de chiffres après la virgule. 

```python 
print(0.1)
print("{:.60f}".format(0.1))
```

En revanche les nombres exprimables en puissances de 2 sont exactement représentés :

```python
print("{:.60f}".format(1.0/64.0))
```

#### Problème d'approximation

En théorie il *pourrait* y  avoir une infinité de chiffres après la virgule pour le codage d'un décimal et il *devrait* y en avoir une infinité ou pour un rationnel non décimal, ou pour un irrationnel mais en pratique on observe des approximations.

Tous les réels ne sont bien-sur pas représentable. En machine on dispose d'un nombre fini de valeurs. Un réel est associé à son plus proche voisin représentable  en machine. Il se trouve donc dans un interval encadré par deux valeurs codables dans le format.

Cela pose des problèmes de précision lors de calculs arithmétiques.

```python
a = 0.1 + 0.2
b = 0.3
if a == b:
    print("Tout va bien. -_- ")
else:
    print("Python ne sait pas compter ! :-O")

print("a =",  "{:.20f}".format(a))
print("b =",  "{:.20f}".format(b))

```

Les erreurs s'accumulent avec le nombre de calculs. 

```python
print("{:30.20f}".format(sum([0.1]*10)))
print("{:30.20f}".format(sum([0.0001]*10000)))
print("{:30.20f}".format(sum([0.0000001]*10000000)))
```

#### Exercice sur les nombres

On veut écrire une fonction python qui prend en paramètre un nombre flottant positif et qui calcule une approximation de sa racine carrée avec la [Méthode de Héron](https://fr.wikipedia.org/wiki/M%C3%A9thode_de_H%C3%A9ron). Cette méthode est définie comme une suite géométrique par récurrence. 

On souhaite afficher les valeurs intermédiaires calculées durant le calcul de la série. 

L'affichage des nombre se fait avec  une *précision* (en nombre de chiffres décimaux) donné par la fonction [`numpy.finfo`](https://docs.scipy.org/doc/numpy/reference/generated/numpy.finfo.html). On partage la précision entre les chiffres avant et après la virgule et les nombres sont alignés par la virgule (le point de décimal). On utilise la fonction [`str.format()`](https://docs.python.org/fr/3.5/library/string.html#format-string-syntax) pour régler la mise en forme de l'affichage. 

Exemple d'affichage
```python 
racine_carree(1234*1234, epsilon)
```
```text
Estimation de la racine carrée de 1522756 :
             2.000000000000000
        380690.000000000000000
        190346.999994746380253
         95177.499944837662042
         47596.749531142704654
         23814.371194056489912
         11939.156963972840458
          6033.349986314364287
          3142.869893457522267
          1813.690600258979430
          1326.640164750435360
          1237.234562149187695
          1234.004228136125903
          1234.000000007243671
          1234.000000000000000
```

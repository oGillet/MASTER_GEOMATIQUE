# **Géomatique - SIG et Python** 

Les systèmes d’informations géographiques (QGIS, ArcGIS, MapINFO) offrent de nombreux outils pour créer, stocker, afficher et analyser. Sur ce dernier point, les SIG sont très rapidement limités. En effet, le géomaticien/géographe est souvent amené à automatiser une chaîne de traitements géomatiques sur un grand nombre de données et/ou à développer ses propres outils pour répondre à une problématique. Ce cours a pour objectif de présenter le langage Python, le SIG QGIS et les librairies de traitements de l'information géographique associées via la réalisation d'une chaîne de traitements. 

## "Hello World" dans différents langages

> Hello World in Java 
```javascript
class HelloWorld {   
     static public void main( String args[] ) { 
     System.out.println( "Hello World!" );
     }
} 
```
> Hello world in PHP 
```php
<?php 
     echo 'Hello World!’; 
?> 
```
> Hello World in R 
```r
cat("Hello world\n") 
```
>Hello world in Python 3
```python
print ("Hello World") 
```

## Les applications du langage
* Science (Analyse de données, SIG et cartographie, climatologie, intelligence artificielle, ...)
* Modélisation 3D (Jeux vidéos, ...)
* Web(Django, android)
* Autres (Raspberry pi, scripting, ...)

## Pourquoi programmer en Python ?

- Python est un langage 
- Portable 
- Ouvert
- Simple programme à des projet complexe, Complet
- Syntaxe est simple
- Types de données évolués
- POO
- Extensible 
- Et continue à évoluer

C'est un langage de « haut niveau ». Il est plus facile à écrire. L’écriture du programme prend moins de temps. La maintenance et recherche d’erreurs (« bogues ») est plus simple. 

## Pourquoi programmer en géographie ?

- Chaîne des traitements (simples ou complexes)
- Automatisation de certaines tâches
- Réexécution du code
- Création de nouvelles fonctionnalités

Python est un **langage d’extension** pour de nombreux système d'information géographique et logiciel de traitement d'images (ArcGis, Grass, Qgis, GVSig, Idrisi, Erdas …).

Python permet de s'affranchir de ces softs et de réaliser des chaînes de traitements plus ou moins complexes sans SIG ou logiciel de traitements d’images en utilisant certaines librairies python comme GDAL/OGR, rasterio ou shapely.

## Algorithmique
L'algorithmique est l’art d’analyser un problème (simple ou complexe) en identifiant les inputs, les outputs ainsi que les traitements à réaliser. 

Un algorithme décrit une succession d'opérations qui, si elles sont fidèlement exécutées, produiront le résultat désiré.

Un algorithme est une suite d'actions que devra effectuer un automate pour arriver en un temps fini, à un résultat déterminé à partir d'une situation donnée. La suite d'opérations sera composée d'actions élémentaires appelées instructions

## Environnement de développement
Un script Python est un simple fichier texte, avec l’extension « .py ».

- Un simple éditeur de texte (bloc note, Notepad++, Word. . .)
- IDLE : un environnement de développement intégré (IDE de python, spyder, pycharm, atom, …)

***Intérêts d’IDLE*** :
- Coloration syntaxique 
- Complétion (en appuyant sur TAB)
- ...

***Composition d’un IDE***:
- Un terminal Python 
- Un éditeur de script
- Un gestionnaire de variable et de packages

## Les variables
> « En informatique, les variables sont des symboles qui associent un nom (l'identifiant) à une valeur. La valeur peut être de quelque type de données que ce soit. » Wikipédia

L’affectation d’une valeur à une variable se fait par l’opérateur = (nomDelaVariable = valeur).

***Affectations*** : 
- On peut assigner une valeur à plusieurs variables simultanément.
- On peut aussi effectuer des affectations parallèles.

``` python
# affectation simultanée
x = y = 7
x
>>> 7
y 
>>> 7
```
``` python
# affectation parallèles
a, b = 4, 8.33
a
>>> 4
```

## Les noms de variables et mots réservés
- Un nom de variable est une séquence de lettres ou de chiffres qui doit toujours commencer par une lettre.
- Les accents, les cédilles, les espaces et autres caractères spéciaux ne sont pas autorisés (sauf l’underscore.
- La case est significative.
- Ecrire le nom des variables en minuscule.

Certaines mots sont réservés comme global, False, True, as, return, from, if, None, ... (33 au total)

``` python
# nom de variable correct 
nombrePair = 2
epsg = RGF93 
n = 7
msg = "Salut"
pie = 3.14159
```
``` python
# nom de variable incorrect
return = Dataframe(np.random.randn(10,5))
None = 12
1ème valeur = 1000
```

``` python
# afficher les variables
print (epsg)
```

# Typage dynamique
Sous python , il n’est pas nécessaire de définir le type des variables, **typage dynamique**,en opposition au typage statique.

``` c
/* En langage C ou C++ */
int a;
a = 5
```

```python
# typage dynamique (Python) 
a = 5
```

| type | Exemple | 
| -------- | -------- | 
| Nombre     | bool, int, float     | 
| Séquence     | str, list, range, ...     |
| Liste associative     | dictionnaire     |

```python
# type de donnée (integer)
nombrePair = 2  
# type de donnée (String) 
msg = “Salut”
# type de donnée (float)
pi = 3.14159

# conversion des données
nombrePair = 2
# convertir en réel
float (nombrePair)
# convertir en string
str (nombrePair)

# convertir en entier
pi = 3.14159
int (pi)
>>> 3
```
## Les opérateurs

| Type | Exemple |
| -------- | -------- |
| Opérateurs mathématiqueq     | +	-	* 	/ 	// 	 % 	 ** |
| Opérateurs logiques     | and	or	not |
| Opérateurs de comparaison | <	>	<=	>=	==	!=	is	is not |

``` python
nombrePair = 2
155 > nombrePair
>>> True
```

## Contrôle du flux d’exécution
Sauf mentions explicites, les instructions d’un programme s’exécutent les unes après les autres, dans l’ordre où elles ont été écrites à l’intérieur du script.

On parle de séquence d’instructions ou de flux d’exécution.
Il existe également des constructions qui modifient le flux d’exécution, les **instructions de contrôle de flux**. Ces instructions ou blocs d’instructions correspondent à un ensemble d’instructions lues de façon classique (séquentielle), mais qui peut être exécuté une ou plusieurs fois

Deux types d'instruction de contrôle de flux:
- La **sélection** (ou exécution conditionnelle)
- La **répétition** (ou boucle)

```python
# la séquence d'instruction
a, b = 3, 7
a  = b
b = a
print (a,b)
```
Attention à la tabulation, l’indentation fait partie de la syntaxe !

```python
# instruction de contrôle de type sélection
a = 20
if (a > 100):
    print("a sup. a 100")
else:
    print("a inf. a 100")
```
```python
# instruction de contrôle de type répétition
a, b, c = 1, 1, 1
while c < 11 :
    print (b)
    a, b, c = b, a+b, c +1
```

## Exécution conditionnelle
Si nous voulons écrire des scripts utiles, il faut des techniques permettant d’aiguiller 
le déroulement du script dans différentes directions en fonction des circonstances rencontrées
L'idée est de dire que si cette variable a telle valeur alors faire cela sinon ceci. 

```python
# condition est respectée  
a = 10 
if a > 5: 
    a = a + 1 
a 
>>> 11 

# condition n’est pas respectée  
a = 3
if a > 5: 
    a = a + 1 
a 
>>> 3 
```
```python
# condition est respectée  
a = 20
if a > 5: 
    a = a + 1 
else: 
    a = a - 1  
a 
>>> 21 
```

test

| Opérateurs | Signification | 
| -------- | -------- | 
| ==     | égal à     | 
| !=     | différent de     | 
| >     | strictement supérieur à     | 
| >=     | supérieur ou égal à     | 
| <     | strictement inférieur      | 
| <=     | inférieur ou égal à     | 

```python
# tester les conditions
a = 5
a == 5
>>> True
3 == 4
>>> False
v = 7
v > 5 and v < 10
>>> True
v = 11
v > 5 or v > 100
>>> True
a, b, c = 1, 10, 100
a < b < c
>>> True
```

## Les répétitions (boucles)
L’une des tâches que les machines font le mieux est la répétition de tâches identiques sans erreur. 
Elles permettent de répéter une ou plusieurs instructions plusieurs fois. 

- La ***boucle for***: elle permet de faire des itérations sur un élément, comme une chaîne de caractères ou une liste. 

- La ***boucle while***: "while" signifie "Tant que". Pour créer une boucle, il faut donc utiliser ce mot clé suivi d'une condition qui dit quand la boucle s'arrête. 

```python
# instructions répétitives (while)
i = 0 
while i < 2 :  
    print(" Jaime la géographie")
    i = i +1 
>>> J aime la géographie
>>> J aime la géographie
```
```python
# instructions répétitives (for)
v = "GEO"
for lettre in v :  
    print(lettre)
>>> G
>>> E
>>> O
```

## Les listes
Comme les tuples ou les dictionnaires, la liste est simplement un ensemble ordonné d’objets.
Les objets d’une liste peuvent être de différents types.
Les listes peuvent être modifiées (ajout, suppression, …).
Les éléments d’une liste sont accessibles en utilisant un index (entier non négatif basé zéro)

```python
liste = [] # définir une liste vide
liste = [1,2,3] # définir une liste composée de trois éléments
liste
>>> [1,2,3] 
liste.append("ok") #ajouter une valeur à  la liste
liste
>>> [1,2,3, ‘ok’] 
liste[0] #afficher le premier élément
>>> 1
type(liste[0])
>>> int
liste[1] = 2.8 # modifier une valeur
liste
>>> [1,2.8,3,’ok’] 
del liste[3] #supprimer une valeur
liste
>>> [1,2.8,3] 
len(liste) #afficher la longueur de la liste
>>> 3
```
## Commentaires
Un programmeur veille toujours à insérer un grand nombre de commentaires dans ses scripts. 

En procédant ainsi, non seulement il facilite la compréhension de ses algorithmes pour d’autres lecteurs éventuels,
mais encore il se force lui-même à avoir les idées plus claires

Ils permettent également de justifier certains choix ainsi que le fonctionnement de certains blocs. 

```python
# un commentaire sur une ligne

# un commentaire sur plusieurs lignes
# un commentaire sur plusieurs lignes
# un commentaire sur plusieurs lignes  
 
"""
    Autre type de commentaires sur plusieurs lignes, docstrings. 
    Pour la description d’une fonction par exemple.
    C’est une façon pratique d’ajouter de la documentation à des fonctions,
    classes...
    Très utile aux utilisateurs ainsi qu´aux développeurs du code.
"""

def une_fonction():
    """ Fonction inutile et vide pour illustrer la notion de docstring
    """
    return(None)

print (une_fonction.__doc__) # affiche le docstring
help (une_fonction) # idem=
```

## Les fonctions
L’un des concepts les plus importants en programmation est celui de fonction.
Pour faire simple, une fonction est une suite d'instructions que l'on peut appeler avec un nom.

On distingue les fonctions prédéfinies (print, input, …) et les fonctions « originales ».

Les avantages sont nombreux:
- Moins de lignes		
- Modularité accrue		
- Code plus fiable

``` python
# Fonction sans paramètres 
def indique_mon_age():
    Return 30
indique_mon_age()
>>> 30
 
Fonction avec paramètres obligatoires
def augmente_mon_age(a = 1, b):
    Return 30 + a + b 
indique_mon_age(2)
>>> 33
```

## Variable globale et locale
- ***Variable globale***: les variables définies à l’extérieur d’une fonction sont des variables globales. Leur contenu est « visible » de l’intérieur d’une fonction, mais la fonction ne peut pas le modifier.
- ***Variable locale***: variable à l’intérieur d’un corps d’une fonction, ces variables ne sont accessibles qu’à la fonction elle-même,  on parle de variables locales à la fonction

```python
def mask():
    p = 20
    print(p,q)
p, q = 15, 38
mask()
>>> 20 38
print (p,q)
>>> 15 38 

x = “hello”  
def test():
    print(x)
test()
>>> hello
```

## Les packages
Fonctions intégrées (int, float, input, print)
Définition des modules : fichier (extension ’.py’) contenant des définitions de fonctions que l’on peut importer et utiliser dans nos programmes. Les packages correspondent à une collection de modules

- https://stackoverflow.com/
- https://pcjericks.github.io/py-gdalogr-cookbook/
- https://gdal.org/python/

```python=
from math import * # On importe toutes les fonctions du package math  
import pandas  as pd # On importe toutes les fonctions du module pandas sous le nom pd  
from math import cos, sqrt # On importe toutes les fonctions cosinus et racine carré du module math
```
## Découverte de python

```python
# 1 - afficher la version de Python
import sys
print (sys.version)

# 2 - afficher mon nom et mon prenom
print ('Olivier','Gillet')

# 3 - déclarer une variable
epsg = 2154
print (epsg)

# 4 - calculer une moyenne
x = 314
y = 569
moyenne = (x+y)/2
print ('La moyenne est de',moyenne)

# 5 - déclaraton multiple
a, b = 254, 12345
print (a,b)

# 6 - déclaration simultanee
a=b=c=epsg
print(epsg)
print('Le premier SCR est',a,'et le second est',b)

# 7 - créer une liste
epsgCode = [2154,4326,3942]
print (epsgCode)
src = ['rgf93','wgs84','rgf93cc42']
print (src)

# 8 - compter le nombre d'elements dans la liste
nbelements = (len(epsgCode))
print("J'ai",nbelements,'dans ma liste')

# 9 - ajouter un element
epsgCode.append(3944)
epsgCode.append(2154)
print(epsgCode)

# 10 - compter occurences 2154
nb2154=epsgCode.count(2154)
print('il y a',nb2154,'codes 2154')

# 11 - supprimer élément
del epsgCode[4] 

# 12 - renverser une liste
epsgCode.reverse()
print (epsgCode)

# 13 - condition "if"
hauteurEau, maxObserve = 150, 100
if hauteurEau > maxObserve :
    print ("Crue du siecle")
else :
    print ("pas interessant")

# 14 - exécution conditionnelle et répétition (for)
# creer une liste
epsg3000 = []
# boucler sur les éléments de la liste epsgCode 
for code in epsgCode :
    # afficher l'élément
    print (code)
    # condition si sup. 3000, ajouter à la nouvelle liste
    if code > 3000 :
        epsg3000.append(code)
# afficher la nouvelle liste
print (epsg3000)

# 15 - répétition (while) pour supprimer les éléments
while len(epsg3000) > 0 :
    del epsg3000[0]
# afficher la liste
print (len(epsg3000))

# 16 - compter le nombre de T
tmp = 'QGIS et Python'
nbT = tmp.count('t')
print ('Il y a',nbT,'t')

# 17 - remplacer les espaces par des *
tmp='QGIS et Python'
print (tmp.replace(' ','*',2))

# 18 - renverser la chaine de caractère
print ("QGIS et Python"[::-1])

# 19 - trier par ordre croissant la liste
liste = [99,22,587,5,1547,100289,3]
print (sorted(liste))

# 20 - afficher le valeur maximale d'une liste d'entiers
liste = [99,22,587,5,1547,100289,3]
print(max(liste))

# 21 - calculer la moyenne d'âge,le nombre de fille et de garçon ainsi que les 
# prénoms sup. à 6 caractères

# définir les listes
ages = [22,25,26,35,47,41]
prenom = ['pierre', 'paul', 'anna', 'alexandra', 'alexandre', 'laure']
sexe = ['M','M','F','F','M','F']

# calculer et afficher la moyenne d'âge
print ("La moyenne est de =", round(sum(ages) / len(ages)))

#  afficher le nombre de fille et de garçons
print ("il y a ", sexe.count('M') , "homme()s et il y a", sexe.count('F'), 'femme(s)')

# Compter le nombre de caractère
c = 0
for i in prenom:
    if len(i) > 6:
        c = c + 1

print ('il y a',c,'prenoms avec plus de 6 caractères')


# Definir une fonction simple (calcul de moyenne)
def moyenneClasse(x):
    moyenne = round(sum(x) / len(x))
    return moyenne

# Appel de fonction 
xMean = moyenneClasse(x = ages)
print ("La moyenne est de =", xMean)
print ("La moyenne est de =", moyenneClasse(x = ages))
```

## Réaliser le jeu du pendu
```python
# définir une fonction pour obtenir la position d'un caractère dans une chaîne
def find(str, ch):
    for i, ltr in enumerate(str):
        if ltr == ch:
            yield i

print ('########################')
print ('### Le jeu du pendu ####')
print ('########################')

# définir le mot à devinir
mot = 'geographie'

# déclarer d'autres variables nécessaire à l'exécution du code
nbErreur = 10
lettreTrouvee = 0
nextLetter = True
tmp = str('_'*len(mot))

# démarrer le jeu
while nextLetter:

    # demander une lettre
    print('')
    l = input("Entrez une lettre ==> ")
    
    # vérifier si la lettre est dans le mot
    nbLettre = mot.count(str(l))
    if nbLettre > 0:
        lettreTrouvee = lettreTrouvee + nbLettre
        for index in list(find(mot, l)):
            tmp = tmp[:index] + l + tmp[index + 1:]
        print ('oui ' + tmp)
    else :
        nbErreur = nbErreur - 1
        print ('non')
    
    # vérifier le résultat
    if lettreTrouvee == len(mot):
        nextLetter = False
        print ('')
        print ('Gagné ! Le mot est bien ' + mot)
    elif nbErreur == 0:
        print ('')
        print ('Perdu ! Le mot est ' + mot)
        nextLetter = False
```
# **Géomatique - SIG et Python** 

Les systèmes d’informations géographiques (QGIS, ArcGIS, MapINFO) offrent de nombreux outils pour créer, stocker, afficher et analyser. Sur ce dernier point, les SIG sont très rapidement limités. En effet, le géomaticien/géographe est souvent amené à automatiser une chaîne de traitements géomatiques sur un grand nombre de données et/ou à développer ses propres outils pour répondre à une problématique. Ce cours a pour objectif de présenter le langage Python, le SIG QGIS et les librairies de traitements de l'information géographique associées via la réalisation d'une chaîne de traitements. 

## "Hello World" dans différents langages

> Hello World in Java 
```js=
class HelloWorld {   
     static public void main( String args[] ) { 
     System.out.println( "Hello World!" );
     }
} 
```
> Hello world in PHP 
```php=
<?php 
     echo 'Hello World!’; 
?> 
```
> Hello World in R 
```r=
cat("Hello world\n") 
```
>Hello world in Python 3
```python=
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

``` python=
# Affectation simultanée
x = y = 7
x
7
y 
7
```
``` python=
# Affectation parallèles
a, b = 4, 8.33
a
4
```

## Les noms de variables et mots réservés
- Un nom de variable est une séquence de lettres ou de chiffres qui doit toujours commencer par une lettre.
- Les accents, les cédilles, les espaces et autres caractères spéciaux ne sont pas autorisés (sauf l’underscore.
- La case est significative.
- Ecrire le nom des variables en minuscule.

Certaines mots sont réservés comme global, False, True, as, return, from, if, None, ... (33 au total)

``` python=
## Nom de variable correct 
nombrePair = 2
epsg = RGF93 
n = 7
msg = “Salut”
pie = 3.14159
```
``` python=
## Nom de variable incorrect
return = Dataframe(np.random.randn(10,5))
None = 12
1ème valeur = 1000
```

``` python=
## Afficher les variables
print (epsg)
```

# Typage dynamique
Sous python , il n’est pas nécessaire de définir le type des variables, **typage dynamique**,en opposition au typage statique.

``` c=
# En langage C ou C++
int a;
a = 5
```

```python=
## typage dynamique (Python) 
a = 5

## type de donnée (integer)
nombrePair = 2  
## type des données (String) 
msg = “Salut”
## type des données (float)
pi = 3.14159

## conversion des données
nombrePair = 2
## convertir en réel
float (nombrePair)
## convertir en string
str (nombrePair)

## convertir en entier
pi = 3,14159
int (pi)
3
```

## Les opérateurs


| Type | Exemple |
| -------- | -------- |
| Opérateurs mathématiqueq     | +	-	* 	/ 	// 	 % 	 ** |
| Opérateurs logiques     | and	or	not |
| Opérateurs de comparaison | <	>	<=	>=	==	!=	is	is not |

``` python=
nombrePair = 2
155 > nombrePair
True
```

## Contrôle du flux d’exécution
Sauf mentions explicites, les instructions d’un programme s’exécutent les unes après les autres, dans l’ordre où elles ont été écrites à l’intérieur du script.

On parle de séquence d’instructions ou de flux d’exécution.
Il existe également des constructions qui modifient le flux d’exécution, les **instructions de contrôle de flux**.




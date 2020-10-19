# **Géomatique - SIG et Python** 

Les systèmes d’informations géographiques (QGIS, ArcGIS, MapINFO) offrent de nombreux outils pour créer, stocker, afficher et analyser. Sur ce dernier point, les SIG sont très rapidement limités. En effet, le géomaticien/géographe est souvent amené à automatiser une chaîne de traitements géomatiques sur un grand nombre de données et/ou à développer ses propres outils pour répondre à une problématique. Ce cours a pour objectif de présenter le langage Python, le SIG QGIS et les librairies de traitements de l'information géographique associées via la réalisation d'une chaîne de traitements. 

## "Hello World" dans différents langages

> Hello World in Java 
```js
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




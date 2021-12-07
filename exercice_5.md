# Chaîne de traitements de GDAL/OGR

Vous devez réaliser l'exercice du **marcheur ivre**en utilisant

***Objectifs***
- Calculer l'indice de végétation par différence normalisée (NDVI).
- Reprojeter le shapefile de notre zone d'étude (EPSG::4326 vers EPSG::4326).
- Découper l'indice de végétation à partir d'une couche de masquage. 

***Données (Inputs)***
- Image satellitaire Landsat 8 (raster, EPSG::32630)
- Donnée vectorielle délimitant la zone d'étude (vecteur, polygone, EPSG::4326)

***Instructions (outils SIG)***


***Résultats (Outputs)***
- Un shapefile avec une ligne représentant le déplacement de l’animal.
- Un shapefile de points avec plusieurs attributs (ID, datetime, X, Y,speed) comme un GPX


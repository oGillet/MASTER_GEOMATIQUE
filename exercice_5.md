# Chaîne de traitements de GDAL/OGR

***Objectifs : *** Vous devez réaliser l'exercice du **marcheur ivre**. 

***Données (Inputs)***
- Néo-canux issus d'un prétraitement des images satellitaires Landsat 8 (raster, EPSG::2154)
  + NDVI
  + DWV
  + TEMPERATURE
- Modèle Numérique de Terrain du satellite ASTER GDEM (raster, EPSG::2154)

***Instructions (selon raster)***
- Contrainte altitude : Le marcheur accélère en descente et ralentit en montée
- Contrainte température : Le marcheur accélère ou ralentit en fonction de la température des surfaces
- Contrainte DWV : Ce n'est plus un marcheur mais un nageur. Ce dernier doit rejoindre la terre germe
- Contrainte végétation : Si la végétation est dense le marcheur peut décider de faire une pause plus ou moins longue

***Résultats (Outputs)***
- Un shapefile avec une ligne représentant le déplacement du marcheur
- Un shapefile de points avec plusieurs attributs (ID, datetime, X, Y,speed, temperature, elevation, ...) comme un GPX
- Une statistique sur le temps de parcours et la distance parcourue


| ID | dateTime        | X      | Y       |speed |Temperature |
| :--|:---------------:| :-----:| :------:| ----:| ----------:|
| 1  | 24112021 145633 | 381046 | 6557972 |      | 13         |
| 2  | 24112021 145635 | 381048 | 6557985 |6km/h | 12.7       |
| 3  | 24112021 145637 | 381058 | 6557995 |5km/h | 12.8       |

 Votre rendu doit faire deux pages maximum (diagramme des traitements + description des sorties). Vous devez également joindre le script python avec votre rapport.Le tout est à déposer au plus tard le  17 décembre 2021.

![data](images/ALL_DATA.png)

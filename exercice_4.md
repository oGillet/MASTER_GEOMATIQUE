# **Créer une trace "GPS"**

Vous devez réaliser le suivi **fictif** GPS de la divagation d'une araignée dans la salle de cours pendant 1 heure.

- Vitesse d'une araignée : 2 km/h  
- Vitesse en m/s = 2/3.6  
- Distance effectuée par intervalle de temps = 0.5 m  

Les outputs attendus sont:
- Un shapefile avec une ligne représentant le déplacement de l'animal.
- Un shapefile de points avec plusieurs attributs (ID, datetime, X, Y,speed) comme un **GPX**

| ID | dateTime        | X      | Y       |speed |
| :--|:---------------:| :-----:| :------:| ----:|
| 1  | 24112021 145633 | 381046 | 6557972 |
| 2  | 24112021 145635 | 381048 | 6557985 |6km/h |
| 3  | 24112021 145637 | 381058 | 6557995 |5km/h |

**Niveau 2**. Vous devez ajouter des temps de pause et de la fatigue (diminution de la vitesse de déplacement). Ainsi vous pouvez calculer des statistiques telles que l'allure, la distance totale ou la durée totale des pauses.  

**Niveau 3**. Vous pouvez ajouter des obstables. Cela nécessite de créer des objets spatialisés (points, polygones ou lignes) et de réaliser des **géotraitements** comme des zones tampons ou des intersections.  

![spider](images/EXO_MASTER.png)


```python

###########################################################
###########################################################
####                      Package                      ####
###########################################################
###########################################################

# Import les packages
import sys # Pour les informations système
from osgeo import ogr, osr #Package pour lire et traiter un très grand nombre de format de donnees vectorielles
import os # Gestion des fichiers et des dossiers
import random

###########################################################
###########################################################
####                Repertoire courant                 ####
###########################################################
###########################################################

# Fixer le repertoire courant
os.chdir("/home/gilleol2/Bureau")

###########################################################
###########################################################
####                  Ouvrir shapefile                 ####
###########################################################
###########################################################

# Ouvrir le shapefile de notre zone d'etude 
DriverName = "ESRI Shapefile"      # e.g.: GeoJSON, ESRI Shapefile
FileName = "BATI_M2_TD.shp"
driver = ogr.GetDriverByName('ESRI Shapefile')
dataSource = driver.Open(FileName, 0) # 0 means read-only. 1 means writeable.

# Vérifier l'ouveture du shapefile
if dataSource is None:
    print('Could not open %s' % (FileName))
else:
    print('Opened %s' % (FileName))
    layer = dataSource.GetLayer()
    featureCount = layer.GetFeatureCount()
    print("Number of features in %s: %d" % (os.path.basename(FileName),featureCount))  

###########################################################
###########################################################
####              Initialiser la divagation            ####
###########################################################
###########################################################
    
# Obtenir l'extension spatiale de notre shapefile
extent = layer.GetExtent()
xmin, xmax, ymin, ymax = layer.GetExtent()

# Definir la position de départ de notre animal 
startX = random.uniform(xmin,xmax)
startY = random.uniform(ymin,ymax)

# Definir la vitesse de déplacement de l'araignée
# Araignée : 2 km/h
# speed = 2/3.6
distanceSpyder = 0.5

###########################################################
###########################################################
####                  Créer les outputs                ####
###########################################################
###########################################################

# Definir les noms pour les shapefiles
output_point = "points_GPS_animal.shp"
output_line = "line_GPS_animal.shp"

#Verifier si ils existent [Si oui, supprimer les fichiers)
if os.path.exists(output_point):
    driver.DeleteDataSource(output_point)
    
if os.path.exists(output_line):
    driver.DeleteDataSource(output_line)
    
# Creer les donnees avec la methode CreateDataSource
ds_point = driver.CreateDataSource(output_point)
ds_line = driver.CreateDataSource(output_line)

# Verifier la creation des donnees
if ds_point is None :
    print ('Could not create file ', ds_point)
    sys.exit (1)

if ds_line is None :
    print ('Could not create file ', ds_line)
    sys.exit (1)

# Importer le src depuis la librairie osr
spatialref = osr.SpatialReference()  # Fixe le scr
spatialref.ImportFromEPSG(2154)  # RGF93 aka ESPG:2154

# Creer la couche ou le layer avec la methode CreateLayer
layer_point = ds_point.CreateLayer('points_GPS_animal', spatialref, geom_type=ogr.wkbPoint)
layer_line = ds_line.CreateLayer('line_GPS_animal', spatialref, geom_type=ogr.wkbLineString)

#
# Points
###########################################################
###########################################################
# Ajouter un champ dans la table attributaire
# Definir l'attribut (FieldDefn)
fielddef = ogr.FieldDefn("X", ogr.OFTReal)
# Ajouter l'attribut a la table (CreateField)
layer_point.CreateField(fielddef)

# Ajouter un champ dans la table attributaire
# Definir l'attribut (FieldDefn)
fielddef = ogr.FieldDefn("Y", ogr.OFTReal)
# Ajouter l'attribut a la table (CreateField)
layer_point.CreateField(fielddef)

# Affiher les champs de la table attributairem on recupere les champs avec la methode GetLayerDefn
layer_ATTRIBUTS = layer_point.GetLayerDefn()
for i in range(layer_ATTRIBUTS.GetFieldCount()):
    print("Point - Field %d: %s" % ( i+1, layer_ATTRIBUTS.GetFieldDefn(i).GetName()))
###########################################################
###########################################################
#
#


#
# Line
###########################################################
###########################################################
# Ajouter un champ dans la table attributaire
# Definir l'attribut (FieldDefn)
fielddef = ogr.FieldDefn("id", ogr.OFTString)
# Ajouter l'attribut a la table (CreateField)
layer_line.CreateField(fielddef)

# Affiher les champs de la table attributairem on recupere les champs avec la methode GetLayerDefn
layer_ATTRIBUTS = layer_line.GetLayerDefn()
for i in range(layer_ATTRIBUTS.GetFieldCount()):
    print("Line - Field %d: %s" % ( i+1, layer_ATTRIBUTS.GetFieldDefn(i).GetName()))

# Créer l'entité
line = ogr.Geometry(ogr.wkbLineString)
###########################################################
###########################################################
#
#


###########################################################
###########################################################
####               Démarrer la divagation              ####
###########################################################
###########################################################

# Boucler pour parcourir le transect et écrire la données dans le shapefile
# Pour chaque ligne du de la colonne
for j in range(3600):
    
    direction = random.randint(0, 3)
    # 0 = up : 1 = down : 2 = left : 3 = right
    
    if direction == 0:
        if (startY + distanceSpyder)<ymax:
            startY = startY + distanceSpyder
    elif  direction == 1:   
        if (startY - distanceSpyder)>ymin:
            startY = startY - distanceSpyder
    elif  direction == 2:   
        if (startX - distanceSpyder)>xmin:
            startX = startX - distanceSpyder
    elif  direction == 3:   
        if (startX + distanceSpyder)<xmax:
            startX = startX + distanceSpyder
    
    #
    # Points
    ###########################################################
    ###########################################################
    # Créer l'entité
    point = ogr.Geometry(ogr.wkbPoint)
    # Fixer le point dans l'espace
    point.AddPoint(startX, startY)
    # Ajouter les attributs et contruire l'entité 
    featureDefn = layer_point.GetLayerDefn()
    feature = ogr.Feature(featureDefn)
    feature.SetGeometry(point)
    feature.SetField('X', startX)
    feature.SetField('Y', startY)
    layer_point.CreateFeature(feature)
    ###########################################################
    ###########################################################
    #
    #
    
    #
    # Line
    ###########################################################
    ###########################################################
    # Ajouter un point à notre ligne
    line.AddPoint(startX, startY)
    ###########################################################
    ###########################################################
    #
    #
    

#
# Line
###########################################################
###########################################################
# Ajouter les attributs et contruire l'entité 
featureDefn = layer_line.GetLayerDefn()
feature = ogr.Feature(featureDefn)
feature.SetGeometry(line)
feature.SetField('id', 1)
layer_line.CreateFeature(feature)
###########################################################
###########################################################
#
#

# Afficher le nombre d'entites
print('Le shapefile de points a: ' + str(layer_point.GetFeatureCount()) + ' entités')
print('Le shapefile de ligne a: ' + str(layer_line.GetFeatureCount()) + ' entités')

# Supprimer les fichier temporaires
del layer_point, layer_line, ds_point, ds_line

```

# GDAL/OGR
* Cheatsheet GDAL <a href="images/GDAL_CHEATSHEET_2020.pdf" download>Cliquez ici pour le télécharger</a>
* Cheatsheet OGR <a href="images/OGR_CHEATSHEET_2020.pdf" download>Cliquez ici pour le télécharger</a>



``` python
###########################################################
###########################################################
####                     PROJET                        ####
####                                                   ####
###########################################################
###########################################################

###############################################################################
# ==============================================================================
# Import des librairies et des fonctions externes
# ==============================================================================
###############################################################################

import numpy as np
import matplotlib.pyplot as plt
from osgeo import gdal, ogr, osr
import os

###############################################################################
# ==============================================================================
# Script principal
# ==============================================================================
###############################################################################

# Fixer un repertoire de travail
os.chdir("/home/gilleol2/Desktop/MASTER_2")

#Initialiser un driver pour gerer le futur fichier
driver = ogr.GetDriverByName("ESRI Shapefile")

#Definir un nom pour le nouveau shapefile
output = "start.shp"

#Supprimer shapefile si il existe deja
if os.path.exists(output):
    driver.DeleteDataSource(output)
    
#Creer les sources de donnee avec la methode CreateDataSource
ds = driver.CreateDataSource(output)

#Importer le src depuis la librairie osr
spatialref = osr.SpatialReference()  # Fixe le scr
spatialref.ImportFromEPSG(3857)

#Creer la couche ou le layer avec la methode CreateLayer
layer = ds.CreateLayer('Point', spatialref, geom_type=ogr.wkbPoint)

#Ajouter un champ dans la table attributaire
#Definir l'attribut (FieldDefn)
fielddef = ogr.FieldDefn("ID", ogr.OFTInteger)
#Ajouter l'attribut a la table (CreateField)
layer.CreateField(fielddef)

# Créer l'entité
point = ogr.Geometry(ogr.wkbPoint)
# Fixer le point dans l'espace
point.AddPoint(121475.93, 6350115.88)
# Ajouter les attributs et contruire l'entité 
featureDefn = layer.GetLayerDefn()
feature = ogr.Feature(featureDefn)
feature.SetGeometry(point)
feature.SetField('ID', 1)
layer.CreateFeature(feature)
# Supprimer les fichier temporaires
del layer, ds               

iface.addVectorLayer("start.shp", "start", "ogr")

```

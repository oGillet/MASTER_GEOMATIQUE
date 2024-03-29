# Calcul des températures de surface

***Objectifs***
- Calculer les températures de surface.
- Réaliser un transect vertical et horizontal des températures.

***Données (Inputs)***
- Images satellitaires Landsat 8 (PIR, RED, TIR, raster, EPSG::32630)
- Donnée vectorielle délimitant la zone d'étude (vecteur, polygone, EPSG::4326)

***Télécharger les données***  
https://1drv.ms/u/s!ApLF8AhXkXryhdASkc1z7ujhXMO6Rw?e=yULzHJ  
Fichier de métadonnées <a href="images/LC08_L1TP_201028_20171117_20200902_02_T1_MTL.xml" download>Cliquez ici pour le télécharger</a>  

***Télécharger le polygone pour découper les images***  
https://1drv.ms/u/s!ApLF8AhXkXryhclj91_N_eb5bg_s5w?e=yOScxM

***Instructions pour calculer des températures de surface***
1. ETAPE n°1

```
NDVI = (rPIR- rRed) / (rPIR + rRed)
```

2. ETAPE n°2

```
TOA = 0.0003342 * rTIR + 0.1

TOA = ML * Qcal + AL
ML = Band-specific multiplicative rescaling factor from the metadata (RADIANCE_MULT_BAND_x, where x is the band number).
Qcal = corresponds to band 10.
AL = Band-specific additive rescaling factor from the metadata (RADIANCE_ADD_BAND_x, where x is the band number).
```

3. ETAPE n°3

```
BRIGHTNESS = (1321.0789 / ln((774.8853 / rTOA) + 1)) - 273.15

BRIGHTNESS = (K1 / ln((K2 / L) + 1)) - 273.15
K1 = Band-specific thermal conversion constant from the metadata (K1_CONSTANT_BAND_x, where x is the thermal band number).
K2 = Band-specific thermal conversion constant from the metadata (K2_CONSTANT_BAND_x, where x is the thermal band number).
L = TOA
```

4. ETAPE n°4

```
PROPORTIONVEG = sqrt((rNDVI-(-0.0837178))/(0.084035-(-0.0837178)))
```

5. ETAPE n°5

```
EMISSIVITY = 0.004 * rPROPORTIONVEG + 0.986
```

6. ETAPE n°6

```
TEMPERATURE = (rBRIGHTNESS / (1 + (0.00115 * rBRIGHTNESS / 1.4388) * ln(rEMISSIVITY)))
```





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
from osgeo import gdal, ogr
import os

###############################################################################
# ==============================================================================
# Script principal
# ==============================================================================
###############################################################################

# Fixer un repertoire de travail
os.chdir("C:\\Users\\gilleol2\\Desktop\\MASTER_2\\TEMPERATURE\\LA_ROCHELLE_RASTER_L8_WGS84")

# Charger les rasters 
rPIRlayer = gdal.Open("LC08_L1TP_201028_20171117_20171122_01_T1_B5.tif")
rREDlayer = gdal.Open("LC08_L1TP_201028_20171117_20171122_01_T1_B4.tif")

#- Définir des variables pour découper le master
gdal.Warp("B4_clip.tif", rREDlayer, cutlineDSName='LA_ROCHELLE_AREA_POLYGON_UTM30n.shp',cropToCutline=True, dstNodata = 0)

# Convertir la bande PIR en array
aPIRlayer = rPIRlayer.ReadAsArray().astype(np.float32)
# Convertir la bande RED en array
aREDlayer = rREDlayer.ReadAsArray().astype(np.float32)

#Calcul du RVI
aRVIlayer = aPIRlayer/aREDlayer

# On obtient de le nombre de colonnes et de lignes
xPixel, yPixel = np.shape(aRVIlayer)[1], np.shape(aRVIlayer)[0]

# Enregistrement de l'image
# Fixer le type de fichier
driver = gdal.GetDriverByName('GTiff')

# Créer le fichier
data_tif = driver.Create("rvi.tif", xPixel, yPixel, 1, gdal.GDT_Float32)

# Vérifier l'écriture de l'image
if data_tif is None:
    print("Error : impossible to create the file !\n")
    sys.exit(1)

# Récuperer les métadonnées d'une des images
data_tif.SetGeoTransform(rPIRlayer.GetGeoTransform())

# Fixer le système de coordonnées de référence
data_tif.SetProjection(rPIRlayer.GetProjection())

# Ecrire les données
data_tif.GetRasterBand(1).WriteArray(aRVIlayer)

# Fermer le raster
del data_tif
```


# Code pour calculer des températures de surface

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
os.chdir("C:\\Users\\gilleol2\\Desktop\\MASTER_2\\TEMPERATURE\\script_python")

# Decouper les images 
gdal.Warp("B5_clip.tif", "LC08_L1TP_201028_20171117_20171122_01_T1_B5.tif", cutlineDSName='LA_ROCHELLE_AREA_POLYGON_WGS84.shp',cropToCutline=True, dstNodata = 0)
gdal.Warp("B4_clip.tif", "LC08_L1TP_201028_20171117_20171122_01_T1_B4.tif", cutlineDSName='LA_ROCHELLE_AREA_POLYGON_WGS84.shp',cropToCutline=True, dstNodata = 0)
gdal.Warp("B10_clip.tif", "LC08_L1TP_201028_20171117_20171122_01_T1_B10.tif", cutlineDSName='LA_ROCHELLE_AREA_POLYGON_WGS84.shp',cropToCutline=True, dstNodata = 0)

# Charger les rasters 
rPIRlayer = gdal.Open("B5_clip.tif")
rREDlayer = gdal.Open("B4_clip.tif")
rTIRlayer = gdal.Open("B10_clip.tif")

# Convertir la bande PIR en array
aPIRlayer = rPIRlayer.ReadAsArray().astype(np.float32)
print("PIR", np.count_nonzero(np.isnan(aPIRlayer)))
# Convertir la bande RED en array
aREDlayer = rREDlayer.ReadAsArray().astype(np.float32)
print("RED", np.count_nonzero(np.isnan(aREDlayer)))
# Convertir la bande TIR en array
rTIRlayer = rTIRlayer.ReadAsArray().astype(np.float32)
print("TIR", np.count_nonzero(np.isnan(rTIRlayer)))

# NDVI
NDVI = (aPIRlayer- aREDlayer) / (aPIRlayer + aREDlayer)
print("NDVI",np.count_nonzero(np.isnan(NDVI)))
# TOA
TOA = 0.0003342 * rTIRlayer + 0.1
print("TOA",np.count_nonzero(np.isnan(TOA)))
# BRIGHTNESS
BRIGHTNESS = (1321.0789 / np.log((774.8853 / TOA) + 1)) - 273.15
print("BRIGHTNESS",np.count_nonzero(np.isnan(BRIGHTNESS)))
#PROPORTIONVEG
PROPORTIONVEG = np.sqrt((NDVI-(-0.0837178))/(0.084035-(-0.0837178)))
print("PROPORTIONVEG",np.count_nonzero(np.isnan(PROPORTIONVEG)))
#EMISSIVITY
EMISSIVITY = 0.004 * PROPORTIONVEG + 0.986
print("EMISSIVITY",np.count_nonzero(np.isnan(EMISSIVITY)))
#TEMPERATURE
TEMPERATURE = (BRIGHTNESS / (1 + (0.00115 * BRIGHTNESS / 1.4388) * np.log(EMISSIVITY)))
print("TEMPERATURE",np.count_nonzero(np.isnan(TEMPERATURE)))

# On obtient de le nombre de colonnes et de lignes
xPixel, yPixel = np.shape(aREDlayer)[1], np.shape(aREDlayer)[0]

# Enregistrement de l'image
# Fixer le type de fichier
driver = gdal.GetDriverByName('GTiff')

# Créer le fichier
data_tif = driver.Create("temperature.tif", xPixel, yPixel, 1, gdal.GDT_Float32)

# Vérifier l'écriture de l'image
if data_tif is None:
    print("Error : impossible to create the file !\n")
    sys.exit(1)

# Récuperer les métadonnées d'une des images
data_tif.SetGeoTransform(rPIRlayer.GetGeoTransform())

# Fixer le système de coordonnées de référence
data_tif.SetProjection(rPIRlayer.GetProjection())

# Ecrire les données
data_tif.GetRasterBand(1).WriteArray(TEMPERATURE)

# Fermer le raster
del data_tif

# Ajouter le raster au QGIS
iface.addRasterLayer("temperature.tif", "temperature")

# Dimension de ma matrice
np.shape(TEMPERATURE)

# Afficher la température d'un pixel
TEMPERATURE[250,650]

cells = []
temp = []
# Afficher la temperature pour la 650eme colonne
for i in range(np.shape(TEMPERATURE)[0]):
    cells.append(i)
    t = TEMPERATURE[i,650]
    temp.append(t)
plt.plot(cells, temp, linestyle='-', marker='o')
plt.show()

# Creer un point en shp
rPIRlayer.GetGeoTransform()

#Initialiser un driver pour gerer le futur fichier
driver = ogr.GetDriverByName("ESRI Shapefile")

#Definir un nom pour le nouveau shapefile
output = "temperature.shp"

#Supprimer shapefile si il existe deja
if os.path.exists(output):
    driver.DeleteDataSource(output)

#Creer les sources de donnee avec la methode CreateDataSource
ds = driver.CreateDataSource(output)

#Importer le src depuis la librairie osr
spatialref = osr.SpatialReference()  # Fixe le scr
spatialref.ImportFromEPSG(32630)

#Creer la couche ou le layer avec la methode CreateLayer
layer = ds.CreateLayer('Point', spatialref, geom_type=ogr.wkbPoint)

#Ajouter un champ dans la table attributaire
#Definir l'attribut (FieldDefn)
fielddef = ogr.FieldDefn("ID", ogr.OFTInteger)
#Ajouter l'attribut a la table (CreateField)
layer.CreateField(fielddef)

fielddef = ogr.FieldDefn("TEMP", ogr.OFTInteger)
layer.CreateField(fielddef)

# Récuperer les métadonnées
originX = rPIRlayer.GetGeoTransform()[0]
pixelWidth = rPIRlayer.GetGeoTransform()[1]  
originY = rPIRlayer.GetGeoTransform()[3]
pixelHeight = rPIRlayer.GetGeoTransform()[5] 

Xcoord = originX+pixelWidth*650
Ycoord = originY+pixelHeight*250

# Créer l'entité
point = ogr.Geometry(ogr.wkbPoint)
# Fixer le point dans l'espace
point.AddPoint(Xcoord, Ycoord)
# Ajouter les attributs et contruire l'entité 
featureDefn = layer.GetLayerDefn()
feature = ogr.Feature(featureDefn)
feature.SetGeometry(point)
feature.SetField('ID', 1)
feature.SetField('TEMP',1)
layer.CreateFeature(feature)
# Supprimer les fichier temporaires
del layer, ds

iface.addVectorLayer("temperature.shp", "temperature", "ogr")

```
# Code pour creer une ligne en shp

``` python
###########################################################
###########################################################
####                  Créer les outputs                ####
###########################################################
###########################################################

# Definir le nom pour le shapefile
output_line = "line_GPS_animal.shp"

#Verifier si ils existent [Si oui, supprimer les fichiers)
if os.path.exists(output_line):
    driver.DeleteDataSource(output_line)
    
# Creer le donnee avec la methode CreateDataSource
ds_line = driver.CreateDataSource(output_line)

# Verifier la creation des donnees
if ds_line is None :
    print ('Could not create file ', ds_line)
    sys.exit (1)

# Importer le src depuis la librairie osr
spatialref = osr.SpatialReference()  # Fixe le scr
spatialref.ImportFromEPSG(2154)  # RGF93 aka ESPG:2154

# Creer la couche ou le layer avec la methode CreateLayer
layer_line = ds_line.CreateLayer('line_GPS_animal', spatialref, geom_type=ogr.wkbLineString)

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

#
# Line
###########################################################
###########################################################
# Ajouter un point à notre ligne
line.AddPoint(startX1, startY1)
# Ajouter un point à notre ligne (une ligne = 2 points ou plus)
line.AddPoint(startX2, startY2)
# Ajouter un dernier point à notre ligne
line.AddPoint(startX3, startY3)
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

# Supprimer les fichier temporaires
del layer_line, ds_line
```

# PyGIS

* Cheatsheet GDAL <a href="images/GDAL_CHEATSHEET_2020.pdf" download>Cliquez ici pour le télécharger</a>
* Cheatsheet OGR <a href="images/OGR_CHEATSHEET_2020.pdf" download>Cliquez ici pour le télécharger</a>

## Données matricielles

```python

###########################################################
###########################################################
####       Manipulation des données matricielles       ####
####                       PyGIS                       ####
###########################################################
###########################################################

# Charger les librairies
import csv, os, sys, datetime, string
import processing
import qgis.utils
from qgis.analysis import QgsRasterCalculator, QgsRasterCalculatorEntry

# Fixer le répertoire courant
os.chdir('/home/gilleol2/Bureau/L8_ROCHELLE')

## Reprojeter le shapefile de la zone d'étude
parameter = {'INPUT': 'LA_ROCHELLE_AREA_POLYGON_WGS84.shp', 'TARGET_CRS': 'EPSG:32630',
                 'OUTPUT': 'LA_ROCHELLE_AREA_POLYGON_UTM30n.shp'}
processing.run('native:reprojectlayer', parameter)

# Charger et afficher le shapefile de la zone d'étude
vZElayer32630 = iface.addVectorLayer("LA_ROCHELLE_AREA_POLYGON_UTM30n.shp", "ZE", "ogr")
    
# Ouvrir le NDVI découpé
rNDVI_CLIPlayer = iface.addRasterLayer('temp_QGIS_clip.tif', "TEMP_CLIP")

# Méthode simple pour charger et afficher le raster 
# Découper le PIR selon le masque
parameter = {'INPUT': "LC08_L1TP_201028_20171117_20171122_01_T1_B5.TIF", 'MASK': vZElayer32630, 'OUTPUT': 'LC08_L1TP_201028_20171117_20171122_01_T1_B5_clip.tif'}
processing.run('gdal:cliprasterbymasklayer', parameter)
rPIRlayer = iface.addRasterLayer("LC08_L1TP_201028_20171117_20171122_01_T1_B5_clip.tif", "PIR")
parameter = {'INPUT': "LC08_L1TP_201028_20171117_20171122_01_T1_B4.TIF", 'MASK': vZElayer32630, 'OUTPUT': 'LC08_L1TP_201028_20171117_20171122_01_T1_B4_clip.tif'}
processing.run('gdal:cliprasterbymasklayer', parameter)
rREDlayer = iface.addRasterLayer("LC08_L1TP_201028_20171117_20171122_01_T1_B4_clip.tif", "RED")
parameter = {'INPUT': "LC08_L1TP_201028_20171117_20171122_01_T1_B10.TIF", 'MASK': vZElayer32630, 'OUTPUT': 'LC08_L1TP_201028_20171117_20171122_01_T1_B10_clip.tif'}
processing.run('gdal:cliprasterbymasklayer', parameter)
rTIRlayer = iface.addRasterLayer("LC08_L1TP_201028_20171117_20171122_01_T1_B10_clip.tif", "TIR")

# Calculer le NDVI
###########################################################
# Définir les entrées de la calculatrice raster
entries=[]

# Définir la première entrée (Bande du ROUGE)
raster1=QgsRasterCalculatorEntry()
# Ajouter un nouvel attribut à l'objet: le nom du raster
raster1.ref='rREDlayer@1'
# Ajouter le raster à ce nouvel objet
raster1.raster=rREDlayer
# Indiquer le nombre de bande
raster1.bandNumber=1
# Ajouter l'objet aux entrées
entries.append(raster1)

# Définir la seconde entrée (Bande du PIR)
raster2=QgsRasterCalculatorEntry()
# Ajouter un nouvel attribut à l'objet: le nom du raster
raster2.ref='rPIRlayer@1'
# Ajouter le raster à ce nouvel objet
raster2.raster=rPIRlayer
# Indiquer le nombre de bande
raster2.bandNumber=1
# Ajouter l'objet aux entrées
entries.append(raster2)

# Définir un ouput
output = "ndvi_QGIS.tif"

# Déclarer certaines variables nécessaires l'exécution de la calculatrice raster
e = rREDlayer.extent()
w = rREDlayer.width()
h = rREDlayer.height()

# Lancer la calculatrice raster
calc=QgsRasterCalculator('(rPIRlayer@1 - rREDlayer@1) / (rPIRlayer@1 + rREDlayer@1)',output,'GTiff',e,w,h,entries)
calc.processCalculation()

# Interpréter les sorties  du QgsRasterCalculator 
# Success = 0
# CreateOutputError = 1
# InputLayerError = 2
# Canceled = 3,
# ParserError = 4
# MemoryError = 5
# BandError = 6 

# Charger et ouvrir le résultat
rNDVIlayer = iface.addRasterLayer(output, "NDVI")


# Calculer le TOA
###########################################################
# Définir les entrées de la calculatrice raster
entries=[]

# Définir la première entrée (Bande du ROUGE)
raster1=QgsRasterCalculatorEntry()
# Ajouter un nouvel attribut à l'objet: le nom du raster
raster1.ref='rTIRlayer@1'
# Ajouter le raster à ce nouvel objet
raster1.raster=rTIRlayer
# Indiquer le nombre de bande
raster1.bandNumber=1
# Ajouter l'objet aux entrées
entries.append(raster1)

# Définir un ouput
output = "toa_QGIS.tif"

# Déclarer certaines variables nécessaires l'exécution de la calculatrice raster
e = rTIRlayer.extent()
w = rTIRlayer.width()
h = rTIRlayer.height()

# Lancer la calculatrice raster
calc=QgsRasterCalculator('0.0003342 * rTIRlayer@1 + 0.1',output,'GTiff',e,w,h,entries)
calc.processCalculation()

# Charger et ouvrir le résultat
rTOAlayer = iface.addRasterLayer(output, "TOA")


# Calculer le BRIGHTNESS
###########################################################
# Définir les entrées de la calculatrice raster
entries=[]

# Définir la première entrée (Bande du ROUGE)
raster1=QgsRasterCalculatorEntry()
# Ajouter un nouvel attribut à l'objet: le nom du raster
raster1.ref='rTOAlayer@1'
# Ajouter le raster à ce nouvel objet
raster1.raster=rTOAlayer
# Indiquer le nombre de bande
raster1.bandNumber=1
# Ajouter l'objet aux entrées
entries.append(raster1)

# Définir un ouput
output = "brightness_QGIS.tif"

# Lancer la calculatrice raster
calc=QgsRasterCalculator('(1321.0789 / ln((774.8853 / rTOAlayer@1) + 1)) - 273.15',output,'GTiff',e,w,h,entries)
calc.processCalculation()

# Charger et ouvrir le résultat
rBRIGHTNESSlayer = iface.addRasterLayer(output, "BRIGHTNESS")


# Calculer le PROPORTIONVEG
###########################################################
# Définir les entrées de la calculatrice raster
entries=[]

# Définir la première entrée (Bande du ROUGE)
raster1=QgsRasterCalculatorEntry()
# Ajouter un nouvel attribut à l'objet: le nom du raster
raster1.ref='rNDVIlayer@1'
# Ajouter le raster à ce nouvel objet
raster1.raster=rNDVIlayer
# Indiquer le nombre de bande
raster1.bandNumber=1
# Ajouter l'objet aux entrées
entries.append(raster1)

# Définir un ouput
output = "proportionVeg_QGIS.tif"

# Déclarer certaines variables nécessaires l'exécution de la calculatrice raster
e = rNDVIlayer.extent()
w = rNDVIlayer.width()
h = rNDVIlayer.height()

# Déclarer certaines variables nécessaires l'exécution de la calculatrice raster
provider = rNDVIlayer.dataProvider()
stats = provider.bandStatistics(1, QgsRasterBandStats.All, rNDVIlayer.extent(), 0)
min = stats.minimumValue
max = stats.maximumValue
print('min:', min,' max:', max)

# Lancer la calculatrice raster
calc=QgsRasterCalculator('sqrt((rNDVIlayer@1-%s)/(%s-%s))'%(min, max, min),output,'GTiff',e,w,h,entries)
calc.processCalculation()
# Charger et ouvrir le résultat
rPROPORTIONVEGlayer = iface.addRasterLayer(output, "PROPORTIONVEG")

# Calculer le EMISSIVITY
###########################################################
# Définir les entrées de la calculatrice raster
entries=[]

# Définir la première entrée (Bande du ROUGE)
raster1=QgsRasterCalculatorEntry()
# Ajouter un nouvel attribut à l'objet: le nom du raster
raster1.ref='rPROPORTIONVEGlayer@1'
# Ajouter le raster à ce nouvel objet
raster1.raster=rPROPORTIONVEGlayer
# Indiquer le nombre de bande
raster1.bandNumber=1
# Ajouter l'objet aux entrées
entries.append(raster1)

# Définir un ouput
output = "emissivity_QGIS.tif"

# Lancer la calculatrice raster
calc=QgsRasterCalculator('0.004 * rPROPORTIONVEGlayer@1 + 0.986',output,'GTiff',e,w,h,entries)
calc.processCalculation()

# Charger et ouvrir le résultat
rEMISSIVITYlayer = iface.addRasterLayer(output, "EMISSIVITY")


# Calculer le TEMPERATURE
###########################################################
# Définir les entrées de la calculatrice raster
entries=[]

# Définir la première entrée (Bande du ROUGE)
raster1=QgsRasterCalculatorEntry()
# Ajouter un nouvel attribut à l'objet: le nom du raster
raster1.ref='rBRIGHTNESSlayer@1'
# Ajouter le raster à ce nouvel objet
raster1.raster=rBRIGHTNESSlayer
# Indiquer le nombre de bande
raster1.bandNumber=1
# Ajouter l'objet aux entrées
entries.append(raster1)

# Définir la seconde entrée (Bande du PIR)
raster2=QgsRasterCalculatorEntry()
# Ajouter un nouvel attribut à l'objet: le nom du raster
raster2.ref='rEMISSIVITYlayer@1'
# Ajouter le raster à ce nouvel objet
raster2.raster=rEMISSIVITYlayer
# Indiquer le nombre de bande
raster2.bandNumber=1
# Ajouter l'objet aux entrées
entries.append(raster2)

# Définir un ouput
output = "temperature_QGIS.tif"

# Lancer la calculatrice raster
calc=QgsRasterCalculator('(rBRIGHTNESSlayer@1 / (1 + (0.00115 * rBRIGHTNESSlayer@1 / 1.4388) * ln(rEMISSIVITYlayer@1)))',output,'GTiff',e,w,h,entries)
calc.processCalculation()

# Charger et ouvrir le résultat
rTEMPERATURElayer = iface.addRasterLayer(output, "TEMPERATURE")

# Reprojeter le NDVI en RGF93
parameters = {"INPUT":'temperature_QGIS.tif',
                      "SOURCE_SRS":"EPSG:32630",
                      "TARGET_CRS":"EPSG:2154",
                      "OUTPUT":"temp_QGIS_2154.tif"}
processing.run("gdal:warpreproject",parameters)

# Ouvrir le NDVI découpé et reprojeté
rNDVI_CLIPlayer = iface.addRasterLayer("temp_QGIS_2154.tif", "temp_QGIS_2154")
codeEpsg = rNDVI_CLIPlayer.crs().authid()
```

```python

###########################################################
###########################################################
####       Manipulation des données vectorielles       ####
####                       PyGIS                       ####
###########################################################
###########################################################

# Charger les librairies
import csv, os, sys, datetime, string
import processing
import qgis.utils
from qgis.analysis import QgsRasterCalculator, QgsRasterCalculatorEntry

# Fixer le répertoire courant
os.chdir('/home/gilleol2/Bureau/L8_ROCHELLE')

# Ouvrir le NDVI
rNDVIlayer = iface.addRasterLayer("ndvi_QGIS.tif", "NDVI")
# Vérifier l'ouverture du raster
if not rNDVIlayer.isValid():
    print("Layer failed to load!")
    
# Reprojeter le NDVI en RGF93
parameters = {"INPUT":'ndvi_QGIS.tif',
                      "SOURCE_SRS":"EPSG:32630",
                      "TARGET_CRS":"EPSG:2154",
                      "OUTPUT":"ndvi_QGIS_2154.tif"}
processing.run("gdal:warpreproject",parameters)

# Ouvrir le NDVI découpé et reprojeté
rNDVI_CLIPlayer = iface.addRasterLayer("ndvi_QGIS_2154.tif", "NDVI_2154")
codeEpsg = rNDVI_CLIPlayer.crs().authid()

# Vérifier la projection du raster
if codeEpsg != 'EPSG:2154':
    print("Wrong EPSG!")

## Données vectorielles
# Afficher le nombre de pixels
w = rNDVI_CLIPlayer.width()
h = rNDVI_CLIPlayer.height()
print('Le nombre de pixel, donc de polygones, est de ', str(w*h))

# Obtenir des informations sur la résulation spatiale du raster
pixelSizeX= rNDVI_CLIPlayer.rasterUnitsPerPixelX()
pixelSizeY = rNDVI_CLIPlayer.rasterUnitsPerPixelY()

# Obetnir l'extension spatiale du raster
spatialExtent = rNDVI_CLIPlayer.extent()
originX, originY = spatialExtent.xMinimum(), spatialExtent.yMaximum()

# Obtenier les valeurs du NDVI 
provider = rNDVI_CLIPlayer.dataProvider()
block = provider.block(1, spatialExtent, w, h)

# Créer un nouveau shapefile, un fichier de points
fields = QgsFields()
# Ajouter les coordonnées du point
fields.append(QgsField("X", QVariant.String))
fields.append(QgsField("Y", QVariant.String))
# Ajouter un attribut avec la valeur du NDVI
fields.append(QgsField("NDVI", QVariant.String))
# Ajouter des attributs issue de la classification
fields.append(QgsField("CLASSIF", QVariant.String))
fields.append(QgsField("TYPE", QVariant.String))

# Créer le shapefile
writer = QgsVectorFileWriter("points_QGIS_ndvi_v.shp", "UTF-8", fields, QgsWkbTypes.Point, rNDVI_CLIPlayer.crs(), driverName="ESRI Shapefile")

# Définir une colonne pour le transect vertical
transect = w/2
# Définir la coordonnée X du transect
Xcoord = originX+pixelSizeX*transect
    
# Boucler pour parcourir le transect et écrire la données dans le shapefile
# Pour chaque ligne du de la colonne
for j in range(h):
    Ycoord = originY-pixelSizeY*(j+0.5)
    # Obtenir la valeur du pixel
    ndviValue = block.value(transect,j)
    # Classifier en fonction de la valer du pixel
    if ndviValue < 0:
        typeClassification, valueClassification = "eau", 1
    elif ndviValue > 0.25:
        typeClassification, valueClassification = "vegetation", 3
    else :
        typeClassification, valueClassification = "mineral", 2
    # Créer un entités
    fet = QgsFeature()
    # Fixer les coordonées du point
    fet.setGeometry(QgsGeometry.fromPointXY(QgsPointXY(Xcoord,Ycoord)))
    # Ajouter les attributs
    fet.setAttributes([Xcoord,Ycoord,ndviValue,typeClassification,valueClassification])
    # Ecrire l'entité dans le shapefile
    writer.addFeature(fet)
  
# Fermer le shapefile
del writer

# Charger et afficher le shapefile
vlayer = iface.addVectorLayer("points_QGIS_ndvi_v.shp", "POINTS", "ogr")
if not vlayer:
    print("Layer failed to load!")
```

# PyGIS

## Données matricielles

> **WARNING**:  
> Les images satellitaires sont celles de Brest et non de La Rochelle. Vous devez modifier les working directory, le nom des images  et les valeurs NDVI max et min /!\

```python
###########################################################
####       Manipulation des données matricielles       ####
####            et vectorielle avec PyGIS              ####
###########################################################

# Charger les librairies
import csv, os, sys, datetime, string
import processing
import qgis.utils
from qgis.analysis import QgsRasterCalculator, QgsRasterCalculatorEntry

# Fixer le répertoire courant
os.chdir('/home/gilleol2/Bureau/LC08_L1TP_203026_20170827_20170914_01_T1')

# Méthode simple pour charger et afficher le raster 
rPIRlayer = iface.addRasterLayer("LC08_L1TP_203026_20170827_20170914_01_T1_B5.TIF", "PIR")
rREDlayer = iface.addRasterLayer("LC08_L1TP_203026_20170827_20170914_01_T1_B4.TIF", "RED")
rTIRlayer = iface.addRasterLayer("LC08_L1TP_203026_20170827_20170914_01_T1_B10.TIF", "TIR")

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

# Lancer la calculatrice raster
calc=QgsRasterCalculator('sqrt((rNDVIlayer@1-(-0.14027367532253))/(1-(-0.14027367532253)))',output,'GTiff',e,w,h,entries)
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

## Reprojeter le shapefile de la zone d'étude
parameter = {'INPUT': 'BREST_AREA_POLYGON_WGS84.shp', 'TARGET_CRS': 'EPSG:32630',
                 'OUTPUT': 'BREST_AREA_POLYGON_UTM30n.shp'}
processing.run('native:reprojectlayer', parameter)

# Charger et afficher le shapefile de la zone d'étude
vZElayer32630 = iface.addVectorLayer("BREST_AREA_POLYGON_UTM30n.shp", "ZE", "ogr")
    
# Découper le NDVI selon le masque
parameter = {'INPUT': rTEMPERATURElayer, 'MASK': vZElayer32630, 'OUTPUT': 'temp_QGIS_clip.tif'}
processing.run('gdal:cliprasterbymasklayer', parameter)

# Ouvrir le NDVI découpé
rNDVI_CLIPlayer = iface.addRasterLayer('temp_QGIS_clip.tif', "TEMP_CLIP")
    
# Reprojeter le NDVI en RGF93
parameters = {"INPUT":'temp_QGIS_clip.tif',
                      "SOURCE_SRS":"EPSG:32630",
                      "TARGET_CRS":"EPSG:2154",
                      "OUTPUT":"temp_QGIS_clip_2154.tif"}
processing.run("gdal:warpreproject",parameters)

# Ouvrir le NDVI découpé et reprojeté
rNDVI_CLIPlayer = iface.addRasterLayer("temp_QGIS_clip_2154.tif", "temp_CLIP_2154")
codeEpsg = rNDVI_CLIPlayer.crs().authid()
```



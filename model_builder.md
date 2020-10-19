# Chaîne de traitements de QGIS

![algo](images/5.png)

```python
###########################################################
####  Manipulation des données matricielles sous QGIS  ####
###########################################################

# Charger les librairies
import csv, os, sys, datetime, string
import processing
import qgis.utils
from qgis.analysis import QgsRasterCalculator, QgsRasterCalculatorEntry

# Définir le nom du raster 
fileName = "C:\\Users\\gilleol1.IRED-19-A1C0\\Desktop\\TD_19102020\\LC08_L1TP_201028_20171117_20171122_01_T1_B5.tif"
# Objet qui contient le contient le chemin d'accès
fileInfo = QFileInfo(fileName)
# Récuperer juste le nom du fichier
baseName = fileInfo.baseName()
# Ouvrir le raster de la zone d'étude de votre choix
rlayer = QgsRasterLayer(fileName, baseName)

#Vérifier l'ouverture du raster
if not rlayer.isValid():
    print("Layer failed to load!")

# Méthode plus simple pour charger et afficher le raster 
rPIRlayer = iface.addRasterLayer("C:\\Users\\gilleol1.IRED-19-A1C0\\Desktop\\TD_19102020\\LC08_L1TP_201028_20171117_20171122_01_T1_B5.TIF", "PIR")
rREDlayer = iface.addRasterLayer("C:\\Users\\gilleol1.IRED-19-A1C0\\Desktop\\TD_19102020\\LC08_L1TP_201028_20171117_20171122_01_T1_B4.TIF", "RED")

# Afficher les dimensions du raster
rREDlayer.width(), rREDlayer.height()
rPIRlayer.width(), rPIRlayer.height()

# Afficher l'extension spatiale de votre raster
# On obtient un objet avec l'extension spatiale
rREDlayer.extent()
# On affiche l'extension spatiale du raser
rREDlayer.extent().toString()

# On afficher le type de raster
# 0 = GrayOrUndefined (single band), 1 = Palette (single band), 2 = Multiband
rREDlayer.rasterType() 
rPIRlayer.rasterType()

# On affiche le nombre de bandes du raster
rREDlayer.bandCount()
rPIRlayer.bandCount()

# Calculer le NDVI
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
output = "C:\\Users\\gilleol1.IRED-19-A1C0\\Desktop\\TD_19102020\\ndvi_QGIS.tif"

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

# Reprojeter le shapefile de la zone d'étude
parameter = {'INPUT': 'C:\\Users\\gilleol1.IRED-19-A1C0\\Desktop\\TD_19102020\\LA_ROCHELLE_AREA_POLYGON_WGS84.shp', 'TARGET_CRS': 'EPSG:32630',
                 'OUTPUT': 'C:\\Users\\gilleol1.IRED-19-A1C0\\Desktop\\TD_19102020\\LA_ROCHELLE_AREA_POLYGON_UTM30n.shp'}
processing.run('native:reprojectlayer', parameter)

# Charger et afficher le shapefile de la zone d'étude
vZElayer32630 = iface.addVectorLayer("C:\\Users\\gilleol1.IRED-19-A1C0\\Desktop\\TD_19102020\\LA_ROCHELLE_AREA_POLYGON_UTM30n.shp", "ZE", "ogr")
if not vZElayer32630:
    print("Layer failed to load!")
    
# Découper le NDVI selon le masque
parameter = {'INPUT': rNDVIlayer, 'MASK': vZElayer32630, 'OUTPUT': 'C:\\Users\\gilleol1.IRED-19-A1C0\\Desktop\\TD_19102020\\ndvi_QGIS_clip.tif'}
processing.run('gdal:cliprasterbymasklayer', parameter)

# Ouvrir le NDVI découpé
rNDVI_CLIPlayer = iface.addRasterLayer('C:\\Users\\gilleol1.IRED-19-A1C0\\Desktop\\TD_19102020\\ndvi_QGIS_clip.tif', "NDVI_CLIP")
# Vérifier l'ouverture du raster
if not rNDVI_CLIPlayer.isValid():
    print("Layer failed to load!")

```

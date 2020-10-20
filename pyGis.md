# PyGIS

## Données matricielles
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
os.chdir('')

# Définir le nom du raster 
fileName = "LC08_L1TP_201028_20171117_20171122_01_T1_B5.tif"
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
rPIRlayer = iface.addRasterLayer("LC08_L1TP_201028_20171117_20171122_01_T1_B5.TIF", "PIR")
rREDlayer = iface.addRasterLayer("LC08_L1TP_201028_20171117_20171122_01_T1_B4.TIF", "RED")

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

# Reprojeter le shapefile de la zone d'étude
parameter = {'INPUT': 'LA_ROCHELLE_AREA_POLYGON_WGS84.shp', 'TARGET_CRS': 'EPSG:32630',
                 'OUTPUT': 'LA_ROCHELLE_AREA_POLYGON_UTM30n.shp'}
processing.run('native:reprojectlayer', parameter)

# Charger et afficher le shapefile de la zone d'étude
vZElayer32630 = iface.addVectorLayer("LA_ROCHELLE_AREA_POLYGON_UTM30n.shp", "ZE", "ogr")
if not vZElayer32630:
    print("Layer failed to load!")
    
# Découper le NDVI selon le masque
parameter = {'INPUT': rNDVIlayer, 'MASK': vZElayer32630, 'OUTPUT': 'ndvi_QGIS_clip.tif'}
processing.run('gdal:cliprasterbymasklayer', parameter)

# Ouvrir le NDVI découpé
rNDVI_CLIPlayer = iface.addRasterLayer('ndvi_QGIS_clip.tif', "NDVI_CLIP")
# Vérifier l'ouverture du raster
if not rNDVI_CLIPlayer.isValid():
    print("Layer failed to load!")
    
# Reprojeter le NDVI en RGF93
parameters = {"INPUT":'ndvi_QGIS_clip.tif',
                      "SOURCE_SRS":"EPSG:32630",
                      "TARGET_CRS":"EPSG:2154",
                      "OUTPUT":"ndvi_QGIS_clip_2154.tif"}
processing.run("gdal:warpreproject",parameters)

# Ouvrir le NDVI découpé et reprojeté
rNDVI_CLIPlayer = iface.addRasterLayer("ndvi_QGIS_clip_2154.tif", "NDVI_CLIP_2154")
codeEpsg = rNDVI_CLIPlayer.crs().authid()

# Vérifier la projection du raster
if codeEpsg != 'EPSG:2154':
    print("Wrong EPSG!")
```
## Données vectorielles
```python
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


#/!\/!\/!\/!\/!\/!\/!\/!\/!\/!\/!\/!\
#/!\/!\/!\/!\/!\/!\/!\/!\/!\/!\/!\/!\
#/!\/!\/!\/!\/!\/!\/!\/!\/!\/!\/!\/!\
# Créer un nouveau shapefile
writer = QgsVectorFileWriter("points_QGIS_ndvi_h.shp", "UTF-8", 
fields, QgsWkbTypes.Point, rNDVI_CLIPlayer.crs(), driverName="ESRI Shapefile")

# Définir une colonne pour le transect vertical
#transect = w/2
transect = h/2
# Définir la coordonnée X du transect
#Xcoord = originX+pixelSizeX*transect
Ycoord = originY-pixelSizeY*transect
    
# Boucler pour parcourir le transect et écrire la données dans le shapefile
# Pour chaque ligne du de la colonne
for j in range(w):
    #Ycoord = originY-pixelSizeY*(j+0.5)
    Xcoord = originX+pixelSizeX*(j-0.5)
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
vlayer = iface.addVectorLayer("points_QGIS_ndvi_h.shp", "POINTS", "ogr")
if not vlayer:
    print("Layer failed to load!")
```


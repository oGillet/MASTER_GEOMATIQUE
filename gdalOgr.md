# GDAL/OGR
* Cheatsheet GDAL <a href="images/GDAL_CHEATSHEET_2020.pdf" download>Cliquez ici pour le télécharger</a>
* Cheatsheet OGR <a href="images/OGR_CHEATSHEET_2020.pdf" download>Cliquez ici pour le télécharger</a>

## Données matricielles et vectorielles
```python
###########################################################
###########################################################
####       Manipulation des données matricielles       ####
####                       PyGIS                       ####
###########################################################
###########################################################

# Charger les librairies
import gdal, ogr, osr
import sys
import numpy as np
import math

# Fixer le répertoire courant
os.chdir('')

# Charger les rasters 
rPIRlayer = gdal.Open("LC08_L1TP_201028_20171117_20171122_01_T1_B5.TIF")
rREDlayer = gdal.Open("LC08_L1TP_201028_20171117_20171122_01_T1_B4.TIF")

#Vérifier l'ouverture des rasters
if rPIRlayer is None:
    print('Could not open data')
    sys.exit(1)

if rREDlayer is None:
    print('Could not open data')
    sys.exit(1)
    
# Afficher certaines informations
print('Les rasters ont ' + str(rPIRlayer.RasterXSize) + ' colonnes')
print('Les rasters ont ' + str(rPIRlayer.RasterYSize) + ' lignes')
print('Les rasters ont ' + str(rPIRlayer.RasterCount) + ' bandes')

# Recuperer le SCR avec la methode GetProjection
rPIRscr = rPIRlayer.GetProjection()
rREDscr = rREDlayer.GetProjection()

# Afficher certaines metadonnees du raster avec la methode GetGeoTransform
# Elle retourne un tuple (!= de la liste)
# (X du point en haut à gauche, largeur du pixel, rotation de l'image, Y du point en haut à gauche, hauteur du pixel, rotation de l'image)
print(rPIRlayer.GetGeoTransform())

# Convertir la bande PIR en array
aPIRlayer = rPIRlayer.ReadAsArray().astype(np.float32)
# Convertir la bande RED en array
aREDlayer = rREDlayer.ReadAsArray().astype(np.float32)

#Calcul du NDVI
NDVI = (aPIRlayer - aREDlayer) / (aPIRlayer + aREDlayer)

# On obtient de le nombre de colonnes et de lignes
xPixel, yPixel = np.shape(NDVI)[1], np.shape(NDVI)[0]
# Enregistrement de l'image
# Fixer le type de fichier
driver = gdal.GetDriverByName('GTiff')
# Créer le fichier
data_tif = driver.Create("ndvi_GDAL.tif", xPixel, yPixel, 1, gdal.GDT_Float32)
# Vérifier l'écriture de l'image
if data_tif is None:
    print("Error : impossible to create the file !\n")
    sys.exit(1)
# Récuperer les métadonnées d'une des images
data_tif.SetGeoTransform(rPIRlayer.GetGeoTransform())
# Fixer le système de coordonnées de référence
data_tif.SetProjection(rPIRscr)
# Ecrire les données
data_tif.GetRasterBand(1).WriteArray(NDVI)
# Fermer le raster
del data_tif

# Ouvrir le NDVI et la bande du thermique
rNDVIlayer = gdal.Open("ndvi_GDAL.tif")

#- Définir des variables pour découper le master
gdal.Warp("ndvi_GDAL_clip.tif", rNDVIlayer, cutlineDSName='LA_ROCHELLE_AREA_POLYGON_UTM30n.shp',cropToCutline=True, dstNodata = 0)


# Définir le système de projection
RGF93 = 'PROJCS["RGF93 / Lambert-93",GEOGCS["RGF93",DATUM["Reseau_Geodesique_Francais_1993",SPHEROID["GRS 1980",6378137,298.257222101,AUTHORITY["EPSG","7019"]],TOWGS84[0,0,0,0,0,0,0],AUTHORITY["EPSG","6171"]],PRIMEM["Greenwich",0,AUTHORITY["EPSG","8901"]],UNIT["degree",0.01745329251994328,AUTHORITY["EPSG","9122"]],AUTHORITY["EPSG","4171"]],UNIT["metre",1,AUTHORITY["EPSG","9001"]],PROJECTION["Lambert_Conformal_Conic_2SP"],PARAMETER["standard_parallel_1",49],PARAMETER["standard_parallel_2",44],PARAMETER["latitude_of_origin",46.5],PARAMETER["central_meridian",3],PARAMETER["false_easting",700000],PARAMETER["false_northing",6600000],AUTHORITY["EPSG","2154"],AXIS["X",EAST],AXIS["Y",NORTH]]'

# Reprojeter le NDVI en RGF93
gdal.Warp('ndvi_GDAL_clip_2154.tif', 'ndvi_GDAL_clip.tif',dstSRS=RGF93)

# Charger le NDVI découpé et reprojeté
rNDVI_CLIPlayer = gdal.Open("ndvi_GDAL_clip_2154.TIF")
# Convertir la bande RED en array
aNDVI_CLIPlayer = rNDVI_CLIPlayer.ReadAsArray().astype(np.float32)

# Recuperer le SCR avec la methode GetProjection
proj = osr.SpatialReference(wkt=rNDVI_CLIPlayer.GetProjection())

# Vérifier la projection du raster
if proj.GetAttrValue('AUTHORITY',1) != '2154':
    print("Wrong EPSG")
    
# Afficher le nombre de pixels
w = rNDVI_CLIPlayer.RasterXSize
h = rNDVI_CLIPlayer.RasterYSize
print('Le nombre de pixel, donc de polygones, est de ', str(w*h))

# Afficher certaines metadonnees du raster avec la methode GetGeoTransform
# Elle retourne un tuple (!= de la liste)
# (X du point en haut à gauche, largeur du pixel, rotation de l'image, Y du point en haut à gauche, hauteur du pixel, rotation de l'image)
originX, pixelWidth, xskew, originY, yskew, pixelHeight  = rNDVI_CLIPlayer.GetGeoTransform()
```
```python
###########################################################
###########################################################
####       Manipulation des données vectorielles       ####
####                       PyGIS                       ####
###########################################################
###########################################################

#Initialiser un driver pour gerer le futur fichier
driver = ogr.GetDriverByName("ESRI Shapefile")

#Definir un nom pour le nouveau shapefile
output = "points_GDAL_ndvi.shp"

#Verifier si ils existent [Si oui, supprimer les fichiers)
if os.path.exists(output):
    driver.DeleteDataSource(output)
    
#Creer les sources de donnee avec la methode CreateDataSource
ds = driver.CreateDataSource(output)

#Verifier la creation de la donnee
if ds is None :
    print ('Could not create file ', ds)
    sys.exit (1)

#Importer le src depuis la librairie osr
spatialref = osr.SpatialReference()  # Fixe le scr
spatialref.ImportFromEPSG(2154)  # RGF93 aka ESPG:2154

#Creer la couche ou le layer avec la methode CreateLayer
layer = ds.CreateLayer('LINE', spatialref, geom_type=ogr.wkbPoint)

#Ajouter un champ dans la table attributaire
#Definir l'attribut (FieldDefn)
fielddef = ogr.FieldDefn("X", ogr.OFTReal)
#Ajouter l'attribut a la table (CreateField)
layer.CreateField(fielddef)

fielddef = ogr.FieldDefn("Y", ogr.OFTReal)
layer.CreateField(fielddef)

# Ajouter un attribut avec la valeur du NDVI
fielddef = ogr.FieldDefn("NDVI", ogr.OFTReal)
layer.CreateField(fielddef)

# Ajouter des attributs issue de la classification
fielddef = ogr.FieldDefn("CLASSIF", ogr.OFTInteger)
layer.CreateField(fielddef)

fielddef = ogr.FieldDefn("TYPE", ogr.OFTString)
layer.CreateField(fielddef)

#Affiher les champs de la table attributairem on recupere les champs avec la methode GetLayerDefn
layer_ATTRIBUTS = layer.GetLayerDefn()
for i in range(layer_ATTRIBUTS.GetFieldCount()):
    print("Field %d: %s" % ( i+1, layer_ATTRIBUTS.GetFieldDefn(i).GetName()))

# Définir une colonne pour le transect vertical
transect = w/2
# Définir la coordonnée X du transect
Xcoord = originX+pixelWidth*transect

# Boucler pour parcourir le transect et écrire la données dans le shapefile
# Pour chaque ligne du de la colonne
for j in range(h):
    #Determiner les coordonnes du point
    Ycoord = originY+pixelHeight*(j+0.5)
    # Obtenir la valeur du pixel
    ndviValue = aNDVI_CLIPlayer[int(transect),j]
    # Classifier en fonction de la valer du pixel
    if ndviValue < 0:
        typeClassification, valueClassification = "eau", 1
    elif ndviValue > 0.25:
        typeClassification, valueClassification = "vegetation", 3
    else :
        typeClassification, valueClassification = "mineral", 2
    # Créer l'entité
    point = ogr.Geometry(ogr.wkbPoint)
    # Fixer le point dans l'espace
    point.AddPoint(Xcoord, Ycoord)
    # Ajouter les attributs et contruire l'entité 
    featureDefn = layer.GetLayerDefn()
    feature = ogr.Feature(featureDefn)
    feature.SetGeometry(point)
    feature.SetField('X', Xcoord)
    feature.SetField('Y', Ycoord)
    feature.SetField('NDVI', float(ndviValue))
    feature.SetField('CLASSIF', valueClassification)
    feature.SetField('TYPE', typeClassification)
    layer.CreateFeature(feature)

# Afficher le nombre d'entites
print('Le shapefile a: ' + str(layer.GetFeatureCount()) + ' entités')

# Supprimer les fichier temporaires
del layer, ds
```

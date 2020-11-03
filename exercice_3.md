# Calcul des températures de surface

***Objectifs***
- Calculer les températures de surface.
- Réaliser un transect vertical et horizontal des températures.

***Données (Inputs)***
- Images satellitaires Landsat 8 (PIR, RED, TIR, raster, EPSG::32630)
- Donnée vectorielle délimitant la zone d'étude (vecteur, polygone, EPSG::4326)

***Instructions pour calculer des températures de surface***
1. ETAPE n°1

```
NDVI = (rPIR- rRed) / (rPIR + rRed)
```

2. ETAPE n°2

```
TOA = 0.0003342 * rTIR + 0.1
```

3. ETAPE n°3

```
BRIGHTNESS = 1321.0789 / ln((774.8853 / rTOA) + 1)) - 273.15
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

***Résultats attendus (Outputs)***
- Un bref rapport présentant la chaîne de traitements
- Le model builder pour calculer les températures de surface seulement (fichier en .model3)
- Le script python pour calculer les températures de surface seulement et créer les transects (PyGIS ou GDAL/OGR)
- Les températures de surface sur La Rochelle (donnée matricielle découpée et reprojetée, EPSG::2154)
- Transect verticale et horizontal des températures de surface sur la zone d'étude réduite (données vetorielles avec 4 attributs, EPSG::2154)

| ID  | X      | Y       | TEMPERATURE    |
| --- | ------ | ------- | -------------- |
| 1   | 642034 | 1761994 |  29            |

```python
# Charger les librairies
import gdal, ogr, osr
import sys
import numpy as np
import os

def saveRaster(data, filename, resolution, coordX, coordY, scr_projection):
    """
    Fonction pour sauvergarder des tableaux a 2 dimension en raster
    data => tableau a sauvegarder
    resolution => resolution spatiale du raster
    coordX/coordY => coordonnees du point point en haut a gauche
    xPixel => nombres de lignes
    yPixel => nombres de colonnes
    scr_projection => un système de projection
    Cette fonction a 6 parametres obligatoires et ne retourne rien

    """
    # Recuperer certaines informations importantes
    xPixel = np.shape(data)[1]
    yPixel = np.shape(data)[0]
    # Enregistrement de l'image
    driver = gdal.GetDriverByName('GTiff')
    data_tf = driver.Create(filename, xPixel, yPixel, 1, gdal.GDT_Float32)
    if data_tif is None:
        print("Error : impossible to create the file !\n")
        sys.exit(1)
    geotransform = (coordX, resolution, 0, coordY, 0, -resolution)
    # Reprojection de l'image
    data_tif.SetGeoTransform(geotransform)
    # Defnition du SCR
    data_tif.SetProjection(scr_projection)
    # Ecriture du raster
    data_tif.GetRasterBand(1).WriteArray(data)
    # Fermeture du fichier
    del data_tif
    print('Le fichier ' + filename + ' est sauvegardé')

# Fixer le répertoire courant
os.chdir("/!\/!\/!\/!\/!\/!\/!\/!\/!\/!\\")

# Charger les rasters 
rPIRlayer = gdal.Open("LC08_L1TP_201028_20171117_20171122_01_T1_B5.TIF")
rREDlayer = gdal.Open("LC08_L1TP_201028_20171117_20171122_01_T1_B4.TIF")
rTIRlayer = gdal.Open("LC08_L1TP_201028_20171117_20171122_01_T1_B5.TIF")

#Vérifier l'ouverture des rasters
if rPIRlayer is None or rREDlayer is None or rTIRlayer is None:
    print('Could not open data')
    sys.exit(1)

#Obtenir les métadonnées des images satellitaires
upperLeftx, pixelWidth, xskew, upperLefty, yskew, pixelHeight = rREDlayer.GetGeoTransform()
rSCR = rPIRlayer.GetProjection()

# Convertir la bande PIR en array
aPIRlayer = rPIRlayer.ReadAsArray().astype(np.float32)
# Convertir la bande RED en array
aREDlayer = rREDlayer.ReadAsArray().astype(np.float32)
# Convertir la bande RED en array
aTIRlayer = rTIRlayer.ReadAsArray().astype(np.float32)

#Calcul du NDVI
NDVI = (aPIRlayer - aREDlayer) / (aPIRlayer + aREDlayer)

#Sauvegarder le NDVI
saveRaster(data=NDVI, resolution=pixelWidth,
           filename='NDVI.tif', coordX=upperLeftx, coordY=upperLefty,
           scr_projection=rSCR)
           
#Calcul du NDVI
TOA = 0.0003342 * aTIRlayer + 0.1

#Sauvegarder le TOA
saveRaster(data=TOA, resolution=pixelWidth,
           filename='TOA.tif', coordX=upperLeftx, coordY=upperLefty,
           scr_projection=rSCR)
```



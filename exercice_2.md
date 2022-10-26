# Exercices
## Jeu du + ou +
``` python
# Importer les librairies
from random import randint

# Generer un nombre aleatoire et d'autres variable
nbAdeviner = randint(1,200)
chercheToujours = True
c = 0

# Demarrer la boucle
while (chercheToujours):
    print('')
    # Entrezun chiffre
    n = int(input("Entrez un nombre ==> "))
    # Agir en fonction du résultat
    if n > nbAdeviner:
        print("c'est moins !")
        c += 1
    elif n < nbAdeviner:
        print("c'est plus !")
        c += 1
    else:
        print ('Bingo, vous avez trouvé en '+str(c + 1)+' coup(s)')
```

``` python
# Import packages
import numpy as np #Package scientifique
from matplotlib.pyplot import contourf, show
from random import randint

# Initial
gridSize = 30 # Def grid size 
grid_move = np.zeros((gridSize, gridSize)) # Create grid
position_fourmi = [gridSize/2,gridSize/2]  # Ant location
nOfMoves = 100 # Number of move

# Start loop
for move in range(nOfMoves):
    # Display information 
    print('Move '+ str(move))
    # Choose a random number (up, down, left, right)
    direction = randint(1,4)
    
    #####################################################################
    # Change the color of a specific cell
    if(grid_move[int(position_fourmi[0]),int(position_fourmi[1])] == 0.0):
        grid_move[int(position_fourmi[0]),int(position_fourmi[1])] = 1.0
    else :
        grid_move[int(position_fourmi[0]),int(position_fourmi[1])] = 0.0
    #####################################################################
    
    #####################################################################
    # Update the location 
    if direction == 1: #up
        position_fourmi[0] += 1
    elif direction == 2: # down
        position_fourmi[0] -= 1
    elif direction == 3: # left
        position_fourmi[1] -= 1
    else : # right
        position_fourmi[1] += 1
    #####################################################################
levels = [-0.5, 0.5, 1.5]
contourf(grid_move, levels, colors=('white','black'))
show()
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
import numpy as np #Package scientifique
from matplotlib.pyplot import contourf, show
from random import randint
from osgeo import gdal, ogr

###############################################################################
# ==============================================================================
# Script principal
# ==============================================================================
###############################################################################

# Charger les rasters 
rMIRlayer = gdal.Open("C:\\Users\\gilleol2\\Desktop\\PROJET_M2\\LC08_L1TP_201028_20171117_20171122_01_T1_B7.TIF")
rREDlayer = gdal.Open("C:\\Users\\gilleol2\\Desktop\\PROJET_M2\\LC08_L1TP_201028_20171117_20171122_01_T1_B4.TIF")

#Vérifier l'ouverture des rasters
if rMIRlayer is None:
    print('Could not open data')
    sys.exit(1)

if rREDlayer is None:
    print('Could not open data')
    sys.exit(1)
    
# Afficher certaines informations
print('Les rasters ont ' + str(rMIRlayer.RasterXSize) + ' colonnes')
print('Les rasters ont ' + str(rMIRlayer.RasterYSize) + ' lignes')
print('Les rasters ont ' + str(rMIRlayer.RasterCount) + ' bandes')

# Recuperer le SCR avec la methode GetProjection
rMIRscr = rMIRlayer.GetProjection()
rREDscr = rREDlayer.GetProjection()

# Afficher certaines metadonnees du raster avec la methode GetGeoTransform
# Elle retourne un tuple (!= de la liste)
# (X du point en haut à gauche, largeur du pixel, rotation de l'image, Y du point en haut à gauche, hauteur du pixel, rotation de l'image)
print(rREDlayer.GetGeoTransform())

# Convertir la bande PIR en array
aMIRlayer = rMIRlayer.ReadAsArray().astype(np.float32)
# Convertir la bande RED en array
aREDlayer = rREDlayer.ReadAsArray().astype(np.float32)

#Calcul du NDVI
NDWV = (aREDlayer - aMIRlayer) / (aREDlayer + aMIRlayer)

rMIRlayer.GetGeoTransform()

# On obtient de le nombre de colonnes et de lignes
xPixel, yPixel = np.shape(NDWV)[1], np.shape(NDWV)[0]
# Enregistrement de l'image
# Fixer le type de fichier
driver = gdal.GetDriverByName('GTiff')
# Créer le fichier
data_tif = driver.Create("C:\\Users\\gilleol2\\Desktop\\PROJET_M2\\ndwv.tif", xPixel, yPixel, 1, gdal.GDT_Float32)
# Vérifier l'écriture de l'image
if data_tif is None:
    print("Error : impossible to create the file !\n")
    sys.exit(1)
# Récuperer les métadonnées d'une des images
data_tif.SetGeoTransform(rMIRlayer.GetGeoTransform())
# Fixer le système de coordonnées de référence
data_tif.SetProjection(rMIRscr)
# Ecrire les données
data_tif.GetRasterBand(1).WriteArray(NDWV)
# Fermer le raster
del data_tif

# Afficher le raster dans QGIS
iface.addRasterLayer("C:\\Users\\gilleol2\\Desktop\\PROJET_M2\\ndwv.tif", "NDWV")

# Découper les rasters
gdal.Warp("C:\\Users\\gilleol2\\Desktop\\PROJET_M2\\ndwv_clip.tif", "C:\\Users\\gilleol2\\Desktop\\PROJET_M2\\ndwv.tif", cutlineDSName='C:\\Users\\gilleol2\\Desktop\\PROJET_M2\\LA_ROCHELLE_AREA_POLYGON_UTM30n.shp', cropToCutline=True, dstNodata = 0)

# Afficher le raster dans QGIS
iface.addRasterLayer("C:\\Users\\gilleol2\\Desktop\\PROJET_M2\\ndwv_clip.tif", "NDWV_clip")

```

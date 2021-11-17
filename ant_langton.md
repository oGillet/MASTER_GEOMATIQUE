###########################################################
###########################################################
####                    Jeu de la vie                  ####
####                                                   ####
###########################################################
###########################################################

###############################################################################
# ==============================================================================
# Import des librairies et des fonctions externes
# ==============================================================================
###############################################################################

import numpy as np #Package scientifique
import sys # Pour les informations système
from osgeo import gdal, ogr, osr #Package pour lire et traiter un très grand nombre de format de donnees matricielles
import os # Gestion des fichiers et des dossiers
from matplotlib.pyplot import contourf, show

# Fixer le répertoire courant
os.chdir('/home/gilleol2/Bureau/L8_ROCHELLE')

###############################################################################
# ==============================================================================
# Définition locale de fonctions
# ==============================================================================
###############################################################################

##
##
#/!\/!\/!\/!\/!\/!\/!\ Debut de la 1ere fonction /!\/!\/!\/!\/!\/!\/!\
# Fonction pour diriger et deplacer la fourmi
# La fourmi se deplace à droite ou à gauche
def turn_move(initPos, initDirec, turn, stepno):
    #Definir la nouvelle direction
    newdirection = (initDirec + turn) % 4
    #Agir en fonction de la nouvelle direction pour deplacer la fourmi
    if newdirection == 0:
        initPos[0] += 1 #On deplace la fourmi vers le haut
    elif newdirection == 1:
        initPos[1] += 1 #On deplace la fourmi a droite
    elif newdirection == 2:
        initPos[0] -= 1 #On deplace la fourmi vers le bas
    elif newdirection == 3:
        initPos[1] -= 1 #On deplace la fourmi a gauche
    else:
        raise ValueError("Invalid direction-number at step {0}.".format(stepno))
    return (initPos, newdirection)
#/!\/!\/!\/!\/!\/!\/!\ Fin de la 1ere fonction /!\/!\/!\/!\/!\/!\/!\
##
##

###############################################################################
# ==============================================================================
# Definir certaines variables
# ==============================================================================
###############################################################################

#Definir le nombre de mouvements
#100 iterations ==> 20x20 (taille de grille)
#500 iterations ==> 40x40 (taille de grille)
#10000 iterations ==> 80x80 (taille de grille)
#12000 iterations ==> 120x120 (taille de grille)
maxN = 12000

#Taille de la grille
gridSize  = 120

#Creer la grille
grid_move = np.zeros((gridSize, gridSize))
grid_count = np.zeros((gridSize, gridSize))

#Initialiser la position de la fourmi
position = [gridSize/2, gridSize/2]

# La fourmi peut avoir 4 direction
# La direction initiale est vers le haut.
# La direction % 4 nous donne 0=up, 1=left, 2=down, 3=right
direction = 0

###############################################################################
# ==============================================================================
# Demarrer la simulation
# ==============================================================================
###############################################################################

for step in range(0, maxN):
    #Si la couleur de la cellule est blanche
    if grid_move[int(position[0]),int(position[1])] == 0:
        #Premiere etapes, la cellule change d'etat
        grid_move[int(position[0]),int(position[1])] = 1
        #Deuxieme etape, on appelle une fonction pour deplacer la fourmi
        position, direction = turn_move(position, direction, 1, step)
    #Ou si la couleur de la cellule est noire
    elif grid_move[int(position[0]),int(position[1])] == 1:
        #Premiere etape, la cellule change d'etat
        grid_move[int(position[0]),int(position[1])] = 0
        #Deuxieme etape, on appelle une fonction pour deplacer la fourmi
        position, direction = turn_move(position, direction, -1, step)
    #Sinon il y a une erreur
    else:
        #Le mot-cle raise permet de lever une exception
        raise ValueError("Invalid value at grid position y={0}, x={1}; occurred during step {2}.".format(position[0], position[1], step))
    grid_count[int(position[0]),int(position[1])] += 1

del direction,gridSize,maxN,step,position

levels = [-0.5, 0.5, 1.5]
contourf(grid_move, levels, colors=('white','black'))
show()

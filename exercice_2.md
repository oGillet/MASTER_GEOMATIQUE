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

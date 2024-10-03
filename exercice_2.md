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

## Amélioration du jeu du + ou du - 

Limiter le nombre de tentatives à 10 essais maximum. Après 10 essais, si l’utilisateur n’a pas trouvé, le programme indique qu’il a perdu et révèle le nombre.
Permettre à l'utilisateur de choisir la plage de nombres (par exemple, entre 1 et 1000).

## Exercice du marcheur ivre 
 

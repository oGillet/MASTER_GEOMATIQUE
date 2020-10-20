# Exercices
## Jeu du pendu
```python
# définir une fonction pour obtenir la position d'un caractère dans une chaîne
def find(str, ch):
    for i, ltr in enumerate(str):
        if ltr == ch:
            yield i

print ('########################')
print ('### Le jeu du pendu ####')
print ('########################')

# définir le mot à devinir
mot = 'geographie'

# déclarer d'autres variables nécessaires à l'exécution du code
nbErreur = 10
lettreTrouvee = 0
nextLetter = True
tmp = str('_'*len(mot))

# démarrer le jeu
while nextLetter:

    # demander une lettre
    print('')
    l = input("Entrez une lettre ==> ")
    
    # vérifier si la lettre est dans le mot
    nbLettre = mot.count(str(l))
    if nbLettre > 0:
        lettreTrouvee = lettreTrouvee + nbLettre
        for index in list(find(mot, l)):
            tmp = tmp[:index] + l + tmp[index + 1:]
        print ('oui ' + tmp)
    else :
        nbErreur = nbErreur - 1
        print ('non')
    
    # vérifier le résultat
    if lettreTrouvee == len(mot):
        nextLetter = False
        print ('')
        print ('Gagné ! Le mot est bien ' + mot)
    elif nbErreur == 0:
        print ('')
        print ('Perdu ! Le mot est ' + mot)
        nextLetter = False
```
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
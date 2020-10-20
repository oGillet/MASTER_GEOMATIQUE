# Exercice n°1 - Découverte de python

```python
# 1 - afficher la version de Python
import sys
print (sys.version)

# 2 - afficher mon nom et mon prenom
print ('Olivier','Gillet')

# 3 - déclarer une variable
epsg = 2154
print (epsg)

# 4 - calculer une moyenne
x = 314
y = 569
moyenne = (x+y)/2
print ('La moyenne est de',moyenne)

# 5 - déclaraton multiple
a, b = 254, 12345
print (a,b)

# 6 - déclaration simultanee
a=b=c=epsg
print(epsg)
print('Le premier SCR est',a,'et le second est',b)

# 7 - créer une liste
epsgCode = [2154,4326,3942]
print (epsgCode)
src = ['rgf93','wgs84','rgf93cc42']
print (src)

# 8 - compter le nombre d'elements dans la liste
nbelements = (len(epsgCode))
print("J'ai",nbelements,'dans ma liste')

# 9 - ajouter un element
epsgCode.append(3944)
epsgCode.append(2154)
print(epsgCode)

# 10 - compter occurences 2154
nb2154=epsgCode.count(2154)
print('il y a',nb2154,'codes 2154')

# 11 - supprimer élément
del epsgCode[4] 

# 12 - renverser une liste
epsgCode.reverse()
print (epsgCode)

# 13 - condition "if"
hauteurEau, maxObserve = 150, 100
if hauteurEau > maxObserve :
    print ("Crue du siecle")
else :
    print ("pas interessant")

# 14 - exécution conditionnelle et répétition (for)
# creer une liste
epsg3000 = []
# boucler sur les éléments de la liste epsgCode 
for code in epsgCode :
    # afficher l'élément
    print (code)
    # condition si sup. 3000, ajouter à la nouvelle liste
    if code > 3000 :
        epsg3000.append(code)
# afficher la nouvelle liste
print (epsg3000)

# 15 - répétition (while) pour supprimer les éléments
while len(epsg3000) > 0 :
    del epsg3000[0]
# afficher la liste
print (len(epsg3000))

# 16 - compter le nombre de T
tmp = 'QGIS et Python'
nbT = tmp.count('t')
print ('Il y a',nbT,'t')

# 17 - remplacer les espaces par des *
tmp='QGIS et Python'
print (tmp.replace(' ','*',2))

# 18 - renverser la chaine de caractère
print ("QGIS et Python"[::-1])

# 19 - trier par ordre croissant la liste
liste = [99,22,587,5,1547,100289,3]
print (sorted(liste))

# 20 - afficher le valeur maximale d'une liste d'entiers
liste = [99,22,587,5,1547,100289,3]
print(max(liste))

# 21 - calculer la moyenne d'âge,le nombre de fille et de garçon ainsi que les 
# prénoms sup. à 6 caractères

# définir les listes
ages = [22,25,26,35,47,41]
prenom = ['pierre', 'paul', 'anna', 'alexandra', 'alexandre', 'laure']
sexe = ['M','M','F','F','M','F']

# calculer et afficher la moyenne d'âge
print ("La moyenne est de =", round(sum(ages) / len(ages)))

#  afficher le nombre de fille et de garçons
print ("il y a ", sexe.count('M') , "homme()s et il y a", sexe.count('F'), 'femme(s)')

# Compter le nombre de caractère
c = 0
for i in prenom:
    if len(i) > 6:
        c = c + 1

print ('il y a',c,'prenoms avec plus de 6 caractères')


# Definir une fonction simple (calcul de moyenne)
def moyenneClasse(x):
    moyenne = round(sum(x) / len(x))
    return moyenne

# Appel de fonction 
xMean = moyenneClasse(x = ages)
print ("La moyenne est de =", xMean)
print ("La moyenne est de =", moyenneClasse(x = ages))

# Definir une fonction simple (valeur maximale)
def maFonctionMax(x):
    maxValue = 0
    for i in x:
        if i > maxValue:
            maxValue = i
    return maxValue

# Appel de fonction 
print ('La valeur maximale est '+ str(maFonctionMax([1,2,3,1454,5])))
```

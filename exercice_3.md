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
BRIGHTNESS = (1321.0789 / ln((774.8853 / rTOA) + 1)) - 273.15
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

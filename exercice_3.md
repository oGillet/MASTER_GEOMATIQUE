# Calcul des températures de surface

***Objectifs***
- Calculer les températures de surface.
- Réaliser un transect vertical et horizontal des températures.

***Données (Inputs)***
- Images satellitaires Landsat 8 (PIR, RED, TIR, raster, EPSG::32630)
- Donnée vectorielle délimitant la zone d'étude (vecteur, polygone, EPSG::4326)
- 
***Télécharger les données ***
https://1drv.ms/u/s!ApLF8AhXkXryhdASkc1z7ujhXMO6Rw?e=yULzHJ
Fichier de métadonnées <a href="images/LC08_L1TP_201028_20171117_20200902_02_T1_MTL.xml" download>Cliquez ici pour le télécharger</a>

***Instructions pour calculer des températures de surface***
1. ETAPE n°1

```
NDVI = (rPIR- rRed) / (rPIR + rRed)
```

2. ETAPE n°2

```
TOA = 0.0003342 * rTIR + 0.1

TOA = ML * Qcal + AL
ML = Band-specific multiplicative rescaling factor from the metadata (RADIANCE_MULT_BAND_x, where x is the band number).
Qcal = corresponds to band 10.
AL = Band-specific additive rescaling factor from the metadata (RADIANCE_ADD_BAND_x, where x is the band number).
```

3. ETAPE n°3

```
BRIGHTNESS = (1321.0789 / ln((774.8853 / rTOA) + 1)) - 273.15

BRIGHTNESS = (K1 / ln((K2 / L) + 1)) - 273.15
K1 = Band-specific thermal conversion constant from the metadata (K1_CONSTANT_BAND_x, where x is the thermal band number).
K2 = Band-specific thermal conversion constant from the metadata (K2_CONSTANT_BAND_x, where x is the thermal band number).
L = TOA
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

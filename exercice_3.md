# Calcul des températures de surface
1. ETAPE n°1

```
NDVI = (rPIR- rRed) / (rPIR + rRed)
```

2. ETAPE n°2

```
TOA = 0.0003342 * B10 + 0.1
```

3. ETAPE n°3

```
BRIGHTNESS = 1321.0789 / ln((774.8853 / rTOAlayer@1) + 1)) - 273.15
```

4. ETAPE n°4

```
PROPORTIONVEG = sqrt((rNDVI_CLIPlayer@1-(-0.0837178))/(0.084035-(-0.0837178)))
```

5. ETAPE n°5

```
EMISSIVITY = 0.004 * rPROPORTIONVEGlayer@1 + 0.986
```

6. ETAPE n°6

```
TEMPERATURE = (rBRIGHTNESSlayer@1 / (1 + (0.00115 * rBRIGHTNESSlayer@1 / 1.4388) * 
				ln(rEMISSIVITYlayer@1)))
```
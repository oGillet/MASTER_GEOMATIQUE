# **Créer une trace "GPS"**

Vous devez réaliser le suivi **fictif** GPS de la divagation d'une araignée dans la salle de cours pendant 1 heure.

- Vitesse d'une araignée : 2 km/h  
- Vitesse en m/s = 2/3.6  
- Distance effectuée par intervalle de temps = 0.5 m  

Les outputs attendus sont:
- Un shapefile avec une ligne représentant le déplacement de l'animal.
- Un shapefile de points avec plusieurs attributs (ID, datetime, X, Y,speed) comme un **GPX**

| ID | dateTime        | X      | Y       |speed |
| :--|:---------------:| :-----:| :------:| ----:|
| 1  | 24112021 145633 | 381046 | 6557972 |
| 2  | 24112021 145635 | 381048 | 6557985 |6km/h |
| 3  | 24112021 145637 | 381058 | 6557995 |5km/h |

**Niveau 2**. Vous devez ajouter des temps de pause et de la fatigue (diminution de la vitesse de déplacement). Ainsi vous pouvez calculer des statistiques telles que l'allure, la distance totale ou la durée totale des pauses.  

**Niveau 3**. Vous pouvez ajouter des obstables. Cela nécessite de créer des objets spatialisés (points, polygones ou lignes) et de réaliser des **géotraitements** comme des zones tampons ou des intersections.  

![spider](images/EXO_MASTER.png)

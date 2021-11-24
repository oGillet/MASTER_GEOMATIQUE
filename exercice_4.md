# **Créer une trace "GPS"**

Vous devez réaliser le suivi **fictif** GPS de la divagation d'un animal quelconque dans une zone d'étude donnée pendant 24 heures (balise argos par exemple).

Les outputs attendus sont:
- Un shapefile avec une ligne représentant le déplacement de l'animal.
- Un shapefile de points avec plusieurs attributs (ID, datetime, X, Y) comme **GPX**

| ID | dateTime        | X      | Y       |Speed |
| :--|:---------------:| :-----:| :------:| ----:|
| 1  | 24112021 145633 | 381046 | 6557972 |
| 2  | 24112021 145635 | 381048 | 6557985 |6km/h |
| 3  | 24112021 145637 | 381058 | 6557995 |5km/h |

**Niveau 2 de l'exercice**. Vous devez ajouter des temps de pause et de la fatigue (diminution de la vitesse de déplacement). Ainsi vous pouvez calculer des statistiques telles que l'allure, la distance totale ou la durée totale des pauses.  

![TRACES](images/trace_GPS.gif)

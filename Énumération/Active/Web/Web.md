Serveurs commun. IIS, Nginx, Apache

# Cookie
| nom | valeur |
| --- | --- |
| .NET: | ASPSESSIONID\<RANDOM>=<COOKIE_VALUE> |
| PHP: | PHPSESSID=<COOKIE_VALUE> |
|  JAVA: | JSESSION=<COOKIE_VALUE> |

# VHost
hôtes virtuels. Permet de cacher ce qui se trouve derrière. 
* Par IP - Plusieurs interfaces avec divers IP. À l'air indépendant de l'extérieur mais c'est la même machine à l'interne.
* Par nom - Distinction faite au niveau applicatif avec le nom de domaine.
# Fichiers de Crawlers
| nom | description |
| --- | --- |
| robot.txt | Fichier qui liste ce qui est permis et “non-permis” de crawler |
| sitemap.xml | Fichier xml qui liste divers endroits du site |

# Fuzzing
Quelques techniques
* *index.*\* existe presque toujours. Fuzzer les extensions.
* Les noms de domaines
* Vhosts
* Nom des paramètres GET et POST
* Valeurs de paramètres GET et POST
## ffuf
```shell
ffuf -recursion -recursion-depth 1 -u http://192.168.10.10/FUZZ -w /usr/share/wordlists/dirb/common.txt
#noms de domaines
ffuf -w ./SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.academy.htb/
#les VHost
ffuf -w ./vhosts -u http://192.168.10.10 -H "HOST: FUZZ.randomtarget.com" -fs 612
#Params GET
ffuf -w ./SecLists/Discovery/Web-Content/burp-parameter-names.txt -u http://admin.academy.htb:80/admin/admin.php?FUZZ=key -fs xxx
#Params POST
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx
#utiliser curl pour obtenir le résultat
curl http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=key' -H 'Content-Type: application/x-www-form-urlencoded'
#Pour fuzzer répertoire, fichier et extention
ffuf -w ./folders.txt:FOLDERS,./wordlist.txt:WORDLIST,./extensions.txt:EXTENSIONS -u http://192.168.10.10/FOLDERS/WORDLISTEXTENSIONS
```

| *Paramètre* | *Description* |
| --- | --- |
| -w \[liste de mots]:\[variable] | Assigner une variable à une liste. FUZZ par défaut |
| -recursion -recursion-depth 1 | Récursion de pages et répertoires |

## curl
```shell
#Tester Vhost
curl -s http://192.168.10.10 -H "Host: randomtarget.com"
```
## gobuster
```shell
gobuster dir -u http://192.168.50.20:8000 -w /usr/share/wordlists/dirb/common.txt -x html,php
```

# Outils
## Nikto
Bon pour trouver un peu n'importe quoi
```
nikto -host 192.168.176.216
```
## Whatweb
Identify information sur le serveur web
```
whatweb -a3 https://www.facebook.com -v
```
* -a aggression level
* -v verbose
## Wappalyzer
Application de navigateur, identifie les technologies d'un serveur
## WafW00f
identify les WAF
```
wafw00f -v https://www.tesla.com
```
-a 		vérifie tous firewall possibles, n'arrête pas à la première correspondance
## nmap

```shell
#pour obtenir de l'information simple	
sudo nmap -p80  -sV 192.168.50.20
#Énumération de dossiers simple
sudo nmap -p80 --script http-enum 192.168.50.20
```
## debuggeur de navigateur
Information sur les requêtes et réponses (Réseau)
Code (Débugger)
Inspecter un item en particulier (clique droit, inspecter)
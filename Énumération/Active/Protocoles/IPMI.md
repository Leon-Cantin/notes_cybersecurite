Composante physique généralement ARM et Linux qui permet de gérer une machine indépendament de son état. Données directe sur le matériel. Utilise **SNMP**. Requière du courrant et un accès directe à internet.
exposent généralement une **console web, accès distant en ligne de commande** comme Telnet ou SSH et port **UDP 623**

# Énum

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo nmap -sU --script ipmi-version -p 623 10.129.202.5
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Aussi disponible dans msfconsole

# Mot de passe pas défaut
| Product | 			Username 	|	Password |
| --- | --- | --- |
| Dell iDRAC 		|root 			|calvin |
| HP iLO 			|Administrator 	|randomized 8-character string consisting of numbers and uppercase letters|
| Supermicro IPMI 	|ADMIN 			|ADMIN |

# Obtenir hash de mot de passe (IPMI 2.0)
Retourne un hash salté
(Chercher comment)
Utiliser ipmi_dumphashes de metasploit
Cracker avec hashcat mode 7300
Pour un HP iLO par défaut. 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

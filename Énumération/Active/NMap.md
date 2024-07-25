Utiliser avec sudo pour plus d'efficité (utilise SYN scan). Plus petit paquet, sinon utilise l'API Berkeley.
```
nmap -p 80 192.168.50.1-253
```
## Status du port
| Status | Description |
| --- | --- |
| Filtered | Pas de réponse |

# Arguments

| Argument | Description |
| --- | --- |
| -sS | TCP Syn sans Ack |
| -sT | TCP avec Ack (défaut si pas sudo) |
| -sU | UDP, envois aussi paquets protocol spécifique |
| -sn | pour “pinger”. Syn sur 443, Ack sur 80 et ICMP Timestamp. Utiliser sur une plage d'IPs |
| -oG | pour envoyer dans un fichier et grep dessus |
| -p <\port> | |
| --top-ports \<nb> | |
| -A | traceroute, détection OS, script scanning | 
| -O | essaye de deviner l'OS avec indices dans les paquets --osscan-guess pour forcer même si pas précis |

## Scripts
| Argument | Description |
| --- | --- |	
| --script \<scripts ... n> | sont dans /usr/share/nmap/scripts. Utiliser des catégories au lieu de nom de scripts ex: vuln |
| --script-help | |

## Évitement
| Argument | Description |
| --- | --- |
| -g --source-port	\<port> | Port source, utiliser un port commun comme 53. **NSE n'utilise pas ce port** |
| --disable-arp-ping | désactive ARP |
| -Pn |	désactive ICMP |
| -n | pas de résolution DNS |

## Debug
| Argument | Description |
| --- | --- |
| --packet-trace | Info sur les paquets envoyés et reçu |
| --script-trace | Info sur l'exécution des scripts |
| --reason | raison de l'état d'un port |

## Sortie
Utiliser xsltproc sur le format .xml pour un rapport .html	

| Argument | Description |
| --- | --- |
| -oN |	Fichier normal |
| -oG | Greable fichier |
| -oX |	XML |
| -oA |	Tous les formats |

# Ajout de scripts:
1. Ajouter un NSE dans /usr/share/nmap/scripts/ 
2. Mise à jour de script.db après avoir ajouté un script dans le dossier  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo nmap --script-updatedb
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Catégories:
* vuln
* broadcast
* exploit
* intrusive
* dos

# vulners.nse
Script fesant une requête sur une DB web après une détection de service: 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo nmap -sV -p 443 --script vulners 192.168.1.1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

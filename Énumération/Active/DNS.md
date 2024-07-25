# Enregistrements
| Nom | Description |
| --- | --- |
| **NS** | Nameserver, contient le nom de l'autorité. Doit en avoir au moins un. |
| **A** | Host record, nom -> IPv4 |
| **AAAA** | Quad A host record, nom -> IPv6 |
| **MX** | Mail exchange record. Nom du/server serveur de courriels déservant un domaine |
| **PTR** | Pointer Record. Addresse (IPv4) -> nom |
| **CNAME** | Cannonical Name Record. Utilisé pour les alias des autres noms |
| **TXT** | Données arbitraire |
| **SOA** | Info sur la zone et le courriel de l'administrateur |

# Outils
## host
``` bash
host [-t mx,a,aaaa, ...] nom
```
## dnsenum
automatise la reconnaissance DNS
## dnsrecon
automatise la reconnaissance DNS
-d domaine
-t type (std, brt)
-D fichier
## nslookup (windows)
```bash
nslookup [options] domain [DNS] -type=[MX,TXT, ...]
```
## dig
```bash
dig facebook.com @1.1.1.1
#peut parfoit trouver la version du serveur
dig CH TXT version.bind 10.129.120.85
```
## crt.sh
 Site qui cherche les certificats liés à un domaine
## GoBuster
Avec un fichier de schéma on peut trouver des sous-domaines
ex: 	lert-api-shv-{NUMBER}-sin6.facebook.com
Fichier de schéma:
```
lert-api-shv-{GOBUSTER}-sin6
atlas-pp-shv-{GOBUSTER}-sin6
```
```bash
gobuster dns -q -r "d.ns.facebook.com" -d "facebook.com" -w "numbers.txt" -p ./patterns.txt -o "gobuster_${TARGET}.txt"
```
# Tactiques
## Zone transfers
```bash
#identifier les serveurs de noms
nslookup -type=NS zonetransfer.me
#Faire le transfert de zones
nslookup -type=any -query=AXFR zonetransfer.me nsztm1.digi.ninja
```
```shell
dig axfr inlanefreight.htb @10.129.14.128
```
## Brute force
Énumérations à l'aveugle, envoyer des sous-domaines (ftp., www., mail., ...)
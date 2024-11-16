## airmon-ng
Utilitaire pour utiliser le mode moniteur
```shell
sudo airmon-ng start [interface]
sudo airmon-ng stop [interface]
```

## airodump-ng
Permet de lire le traffic
```shell
sudo airodump-ng [interface]
```

| param | définition |
| --- | --- |
| `-c 1,2,..` | choix canaux |
| `--band [a,b,g]` | a=5GHz, b=2.4GHz, g=2.4GHz |
| `-w [nom_fichier]` | écrire vers fichier |

## airgraph-ng
Produit des graphes avec les fichiers de airodump-ng
```shell
sudo airgraph-ng -i HTB-01.csv -g CAPR -o HTB_CAPR.png
```
| param | définition |
| --- | --- |
| `-g [nom]` | type de graph CAPR, CPG |

## aireplay-ng
Génère du traffic. Injection ARP, deauthentification, ...
Tester en premier si injection de paquets est efficace
```shell
sudo aireplay-ng --test wlan0mon
```
### Déauthentification
Ne Fonctionne pas sur WPA3?
```shell
 sudo aireplay-ng --deauth 5 -a <BSSID AP> -c <BSSID Client> wlan0mon
```

## airdecap-ng
décrypte et enlève les en-têtes 802.11 des captures.

* retirer en-tête
```shell
airdecap-ng -b <bssid_ap> <capture-file>
```
* Décrypter WEP
```shell
airdecap-ng -w <WEP-key> <capture-file>
```
* Décrypter WPA
```shell
airdecap-ng -p <passphrase> <capture-file> -e <essid>
```

## aircrack-ng
Craquer les clef WEP et WPA
### WEP
Doit obtenir assez de IV (Initialization vectors) avant. `--ivs` dans airodump-ng pour capturer seulement ceux-là.
```shell
aircrack-ng -K HTB.ivs 
```

### WPA
Besoin d'un "four-way handshake" et d'une attaque par dictionnaire. Peut avoir seulement paquet EAPOL 2 et 3 ou 3 et 4.

```shell
aircrack-ng HTB.pcap -w /opt/wordlist.txt
```
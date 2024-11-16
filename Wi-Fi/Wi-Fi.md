### Composantes importantes d'un test d'intrustion
* Vérifier la force du mot de passe
	* Attaque force brute
* Analyser la configuration pour vulnérabilités
	* 
* Sonder l'infrastructure réseau pour faiblesses
* Tester appareils clients pour faiblesses

## 802.11 et cadre MAC

Carde MAC contient diverses info de transmission et les données.
3 types de cadres généraux:
* Gestion (00): Gestion, contrôle et contrôle de connexions
* Contrôle (01):  Gère la transmission et réception de données. Considéré comme contrôle de qualité
* Données (10): Contient données

### Gestion (00)
* Beacon (1000)
	* Communique la présence d'un point d'accès. ciphers et authentification supporté, SSID, ...
* Requêtes et réponses de sonde (0100, 0101)
	* Permet à un client de découvrir les points d'accès.
* Requête et réponse d'authentification (1011)
	* Envoyé par le client pour s'identifier au point d'accès.
* Association/Réassociation requête et réponse (0000, 0001, 0010, 0011)
	* Après processus d'authentification, le client envoi requête d'association et point d'accès répond.
* Disassociation et déauthentification (1010, 1100)
	* Contient la raison de déconnection du client
	* Sert pour beaucoup d'attaques.
### Authentification
* Ouvert
*  Clef partagé
	* WEP (déprécié)
	* WPA (acceptable) WEP avec encryption TKIP
	* 802.11i/WPA2: Encryption AES
## Interface Wi-Fi
### choix
2.4G et 5G.
1. `IEEE 802.11ac or IEEE 802.11ax support`
2. `Supports at least monitor mode and packet injection`

https://github.com/v1s1t0r1sh3r3/airgeddon/wiki/Cards%20and%20Chipsets
### Configurations
* Changement de puissance
```shell
#TXpower courant
iwconfig
#Montre toutes les TXPower pour la zone
iw reg get
#changer la zone
sudo iw reg set US
#Changer puissance
sudo ifconfig wlan0 down
sudo iwconfig wlan0 txpower 30
sudo ifconfig wlan0 up
```
* Capacités `iw list`
*  Réseaux disponibles `iwlist <interface>`
*  Changement de canal et fréquence 
```shell
iwlist <interface> channel
sudo ifconfig wlan0 down
#change canal et fréquence
sudo iwconfig wlan0 channel <canal>
#changer fréquence directement
sudo iwconfig wlan0 freq "5.52G"
sudo ifconfig wlan0 up
``` 
### Modes d'interface
Fermer et rouvrir l'interface avant de changer de mode
```shell
sudo ifconfig wlan0 down
#changement de mode ici
sudo ifconfig wlan0 up
```
#### Managed
Mode par défaut. Permet de chercher et s'associer à des points d'accès
```shell
sudo iwconfig wlan0 mode managed
#connexion
sudo iwconfig wlan0 essid HTB-Wifi
```

#### Ad-hoc
P2P permet communication PA à PA.
```shell
sudo iwconfig wlan0 mode ad-hoc
sudo iwconfig wlan0 essid HTB-Mesh
```

#### Master
Mode routeur/point d'accès. Doit activer un daemon pour répondre aux clients.

```shell
#Création d'une config
nano open.conf

interface=wlan0
driver=nl80211
ssid=HTB-Hello-World
channel=2
hw_mode=g

#Démarrage
sudo hostapd open.conf
```

#### Mesh
```shell
sudo iw dev wlan0 set type mesh
```

#### Monitor
Capture tout le traffic

## Connexion
### Facile
GUI ou `nmtui` 
### Compliqué
#### WEP
Créer fichier de config
```config
network={
	ssid="HackTheBox"
    key_mgmt=NONE
    wep_key0=3C1C3A3BAB
    wep_tx_keyidx=0
}
```
```shell
sudo wpa_supplicant -c wep.conf -i wlan0
```
Obtenir adresse DHCP
```shell
sudo dhclient wlan0
```

#### WPA
Fichier de config
```config
network={
	ssid="HackMe"
    psk="password123"
}
```
```shell
sudo wpa_supplicant -c wpa.conf -i wlan0
```
Obtenir adresse DHCP
```shell
#Relâcher adresse précédente si nécessaire
sudo dhclient wlan0 -r
sudo dhclient wlan0
```
* Pour WPA3, `key_mgmt=SAE` dans fichier de config

#### WPA Enterprise
Fichier de config
```config
network={
  ssid="HTB-Corp"
  key_mgmt=WPA-EAP
  identity="HTB\Administrator"
  password="Admin@123"
}
```
```shell
sudo wpa_supplicant -c wpa_enterprsie.conf -i wlan0
```
Obtenir adresse IP
* comme WPA
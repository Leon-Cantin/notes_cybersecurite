1. activer mode moniteur `sudo airmon-ng start wlan0`
2. balayer pour trouver réseaux `sudo airodump-ng wlan0mon` La taille du SSID est affiché
3. Le SSID est envoyé en clair lors de l'authentification. Déauthentification possible, sauf WPA3.

## Force brute
Peut aussi utiliser mdk3 avec une liste de mots ou spécifier la longueur du SSID.
 ```usage
mdk3 <interface> p [test_ options]
```

|**Option**|**Description**|
|---|---|
|`-e`|Specify the SSID for probing.|
|`-f`|Read lines from a file for brute-forcing hidden SSIDs.|
|`-t`|Set the MAC address of the target AP.|
|`-s`|Set the speed (Default: unlimited, in Bruteforce mode: 300).|
|`-b`|Use full brute-force mode (recommended for short SSIDs only). This switch is used to show its help screen|
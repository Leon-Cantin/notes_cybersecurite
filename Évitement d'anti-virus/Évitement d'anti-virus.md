# Composantes d'un Anti-Virus
* Language de signature
* Machine Learning engine
* File engine
* memory engine
* network engine
* disassembler
* Emulateur
* Browser plugin

# Méthodes de détection
*  Par signature
*  Par heuristique
* Comportementale
* Machine Learning

## VirusTotal
http://www.virustotal.com
Donne de l'information intéressante sur les virus, exécute dans une VM pour étudier.
Divulgue l'information aux vendeurs

## AntiScan.Me
Comme virustotal mais ne devrait pas divulguer l'information

# Sur disque
## Packers:
Vieille méthode pour réduire la taille d'un programme
Efficace seulement sur les vieux AV
## Obfuscateurs:
Ajoute du code mort, instructions inutiles
Maginalement efficace contre la détection par signature
Versions modernes fonctionnent en temps réel en mémoire
## Crypters:
Encrypte le code et fournis une coquille pour décrypter en mémoire
Seul le code encrypter est sur le disuqe

Pour être efficace il faut combiner anti-rétro, anti-débuggage, évitement de VM.

# En mémoire
moins surveillé par les AV que le disque

## Injection mémoire processus:
OpenProcess -> Handle -> VirtualAllocEx -> WriteProcessMemory -> CreateRemoteThread
## Injection par réflection de DLL
LoadLibrary ne permet pas de charger à partir de la mémoire
Requière une API maison
## Évidage de processus
Lancer un processus non-malicieux suspendu. Retirer l'image du processus et la remplacer par le code malicieux
## Accrochage (hooking)
Modifier le code pour s'accrocher et rediriger vers notre code malicieux
	
# En pratique
Monter une VM qui ressemble à la cible.
Désactiver la soumissions d'échantillions de windows: Windows Security >Virus & threat protection > Manage Settings
Parfois un accès limité à l'internet inhibe les AV et soumissions d'échantillons
Code maison, plus diverse, moin de chance d'avoir une signature

# Scripts
Comme ils ne sont pas structurés comme un binaire, il sont plus difficiles à identifier. Simplement renommer des variables peut être suffisant.

# Automatiser
Utilitaire permettant de camoufler du code malicieux
* Shellter
* VEIL

# Désactiver
Windows (administrateur)
```powershell-session
Set-MpPreference -DisableRealtimeMonitoring $true
```
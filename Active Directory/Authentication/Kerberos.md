# Kerberos
Domain Contrller aussi Key Distribution Center (KDC) **port 88**
## Authentification
1. AS-REQ contient temps encrypté avec un hash dérivé du mot de passe et nom de l'utilisateur
2. Vérifie hash du mot de passe dans ntds.dit. Si temps pas dupliqué alors OK. Envoie une clef de session et un Ticket Grand Ticket (TGT) encrypté avec un hash NTLM du compte krbtgt. Valide pour 10h par défaut

## Requête d'accès au service:
3. Envoie l'utilisateur encrypté, temp encrypté avec la clef de session, le nom de la ressource et le TGT encrypté
	1. The TGT must have a valid timestamp.
	2. The username from the TGS-REQ has to match the username from theTGT.
	3. The client IP address needs to coincide with the TGT IP address.
4. Réponse contient:	
    1. The name of the service for which access has been granted.
	2. A session key to be used between the client and the service.
	3. A service ticket containing the username and group memberships along with the newly-created session key. (encrypté avec le hash du mot de passe du compte du service.
	
5. Requête inclue: nom d'utilisateur et temps encrypté avec la clef de session associé avec le ticket de service inclueant le ticket lui-même
6. Décrypte le ticket avec le hash du mot de passe du compte de service. Utilise la clef de session décrypté pour décrypter le nom d'utilisateur de l'AP-REQ et vérifie si il concorde avec celui dans le ticket de service.

![[Pasted image 20240118104959.png]]


Prêter attention aux paramètres de requêtes. survoler les buttons pour voir les params et liens.

# Linux
utilise '/'
Pour tester:	
* /etc/host
* /etc/passwd

# Windows
Utilise \
Pour tester: C:\Windows\System32\drivers\etc\hosts
On semble pouvoir utiliser C:\ au lieu de revenir en arrière avec ../../

# Contourner filtres
## ../
* ....//
* Encodage %2e%2e%2f
* double encodage %252e%252e%252f
## fin de string
php < 5.3
* null byte pour terminer la string %00
* string longueur max de 4096. Écrire une longue string.
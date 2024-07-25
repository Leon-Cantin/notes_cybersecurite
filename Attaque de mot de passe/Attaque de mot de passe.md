# Liste de mots
* /usr/share/wordlist/fasttrack.txt
* /usr/share/wordlist/rockyou.txt

## Créer une liste de mots
### CeWL
Permet d'extraire une liste de mots en utilisant un site web
```shell-session
cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
wc -l inlane.wordlist
```
* -d  profondeur de la recherche en pages
* -m longueur minimum d'un mot
### Hashcat
```shell-session
hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```

# Creds par défaut
## defaultcreds-cheat-sheet
```creds search tomcat```

# Noms d'utilisateurs
Trouver des noms d'utilisateurs sur linkedin, google dork: ```inlanefreight.com filetype:pdf```
Générateur de noms d'utilisateur: Username Anarchy
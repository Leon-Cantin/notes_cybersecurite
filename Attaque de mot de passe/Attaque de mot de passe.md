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
### CUPP
Pose quelques questions pour générer une liste
```
cupp -i
```
Retirer certains mots de passe
```bash
sed -ri '/^.{,7}$/d' william.txt            # remove shorter than 8
sed -ri '/[!-/:-@\[-`\{-~]+/!d' william.txt # remove no special chars
sed -ri '/[0-9]+/!d' william.txt            # remove no numbers
```

### réduire Rockyou
Ex: doit contenir minuscules, majuscules, chiffres et faire au moins 10 charactères.
```shell
grep '[[:upper:]]' /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt | grep '[[:lower:]]' | grep '[[:digit:]]' | grep -E '.{10}' > custom_wordlist.txt
```

# Creds par défaut
## defaultcreds-cheat-sheet
```creds search tomcat```

# Noms d'utilisateurs
Trouver des noms d'utilisateurs sur linkedin, google dork: ```inlanefreight.com filetype:pdf```
Générateur de noms d'utilisateur: Username Anarchy

# Réinitialisation de mot de passe
Parfois utilise un jeton faible ex `http://weak_reset.htb/reset_password.php?token=7351` Utiliser la force brute.
```shell-session
seq -w 0 9999 > tokens.txt
```
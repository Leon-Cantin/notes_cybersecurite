Recherche d'information dans les meta données des fichiers. 
Utiliser Gobuster pour trouver des extentions ou google [site:example.com filetype:pdf]

# exiftool
extrait meta données des fichiers

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
exiftool -u -a brochure.pdf
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Doit déterminer OS et Navigateur
# Canarytoken
Retourne de l'information sur OS et Navigateur quand utilisateur clique dessus. Peut-être inclu en image pour automatiquement faire requête dessus.
Lit information venant de user-agent et de javascript
https://canarytokens/generate

# Mark of the web
Marque sur un fichier provenant du web, windows demandera une autorisation pour exécuter. Est présent quand on télécharge du web ou du webdav
Si connexion anonyme tombe passive

```shell
passive off
epsv4 off
```


attention aux fichiers cachés ...  ls -lha

Commandes utiles:
	debug
	trace
	status
	
Les noms de groupes et utilisateurs peuvent ressortir par qui possède un fichier

affichage récursif, montre tout si disponible
	ls -R 	

Télécharger tout
	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
wget -m --no-passive ftp://anonymous:anonymous@192.168.101.110
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

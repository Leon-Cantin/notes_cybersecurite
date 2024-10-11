9 Verbes
`GET`,`POST`,`HEAD`,`PUT`,`DELETE`,`OPTIONS`,`PATCH`,`CONNECT`,`TRACE`

## Outrepasser authentification avec mauvaise configuration
Une mauvaise configuration peut ne s'appliquer qu'à certains verbes. ex:
```xml
<Limit GET POST>
    Require valid-user
</Limit>
```
Limite l'accès à un utilisateur authentifié mais seulement pour POST.
## Outrepasser filtres de sécurité
Filtre seulement sur un verbe mais fait une action avec les autres. ex:
```php
$pattern = "/^[A-Za-z\s]+$/";

if(preg_match($pattern, $_GET["code"])) {
    $query = "Select * from ports where port_code like '%" . $_REQUEST["code"] . "%'";
    ...SNIP...
}
```
Filtre seulement sur GET mais peut aussi prendre les paramètres de POST pour faire l'action. Lors d'un POST, GET est vide et dont légitime.
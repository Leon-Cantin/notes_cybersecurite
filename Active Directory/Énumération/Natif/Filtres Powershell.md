
`-filter` sur n'importe quelle cmdlet

```powershell-session
get-ciminstance win32_product -Filter "NOT Vendor like '%Microsoft%'" | fl

Get-ADUser -Filter "name -eq 'sally jones'"
Get-ADUser -Filter {name -eq 'sally jones'}
Get-ADUser -Filter 'name -eq "sally jones"'

Get-ADUser -filter {name -like "joe*"}

Get-ADUser -Filter {adminCount -eq '1' -and DoesNotRequirePreAuth -eq 'True'}

```

| **Filter** | **Meaning** |
| --- | --- |
| -eq | Equal to |
|-le|Less than or equal to|
|-ge|Greater than or equal to|
|-ne|Not equal to|
|-lt|Less than|
|-gt|Greater than|
|-approx|Approximately equal to|
|-bor|Bitwise OR|
|-band|Bitwise AND|
|-recursivematch|Recursive match|
|-like|Like|
|-notlike|Not like|
|-and|Boolean AND|
|-or|Boolean OR|
|-not|Boolean NOT|

### Si champ non null
`-filter 'serviceprincipalname -like "*"'`

## Properties
`-properties prop1,prop2` choix des champs à retourner.
`-properties *` pour toutes.

## Pipe

| **Commande** | **Effet** |
| --- | --- |
| select | sélectionne certaines colonnes |
| where | comme un filtre `where servicePrincipalName -ne $null` |
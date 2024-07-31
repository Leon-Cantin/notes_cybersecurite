Référence RFC: https://datatracker.ietf.org/doc/html/rfc4515

|**Operator**|**Function**|
|---|---|
|`&`|and|
| \| |or|
|`!`|not|
Notation polonaise `(& (..C1..) (..C2..) (..C3..))`

|**Criteria**|**Rule**|**Example**|
|---|---|---|
|Equal to|(attribute=123)|(&(objectclass=user)(displayName=Smith)|
|Not equal to|(!(attribute=123))|!objectClass=group)|
|Present|(attribute=*)|(department=*)|
|Not present|(!(attribute=*))|(!homeDirectory=*)|
|Greater than|(attribute>=123)|(maxStorage>=100000)|
|Less than|(attribute<=123)|(maxStorage<=100000)|
|Approximate match|(attribute~=123)|(sAMAccountName~=Jason)|
|Wildcards|(attribute=*A)|(givenName=*Sam)|

## Object Identifiers (OIDs)

|**Matching rule OID**|**String identifier**|**Description**|
|---|---|---|
|[1.2.840.113556.1.4.803](https://ldapwiki.com/wiki/Wiki.jsp?page=1.2.840.113556.1.4.803)|LDAP_MATCHING_RULE_BIT_AND|A match is found only if all bits from the attribute match the value. This rule is equivalent to a bitwise **AND** operator.|
|[1.2.840.113556.1.4.804](https://ldapwiki.com/wiki/Wiki.jsp?page=1.2.840.113556.1.4.804)|LDAP_MATCHING_RULE_BIT_OR|A match is found if any bits from the attribute match the value. This rule is equivalent to a bitwise **OR** operator.|
|[1.2.840.113556.1.4.1941](https://ldapwiki.com/wiki/Wiki.jsp?page=1.2.840.113556.1.4.1941)|LDAP_MATCHING_RULE_IN_CHAIN|This rule is limited to filters that apply to the DN. This is a special "extended" match operator that walks the chain of ancestry in objects all the way to the root until it finds a match.|

```powershell
(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=2)) 

#Trouve les groupes où Harry Jones est membre
(member:1.2.840.113556.1.4.1941:=CN=Harry Jones,OU=Network Ops,OU=IT,OU=Employees,DC=INLANEFREIGHT,DC=LOCAL)

```

## searchscope searchbase
réduit la portée de la recherche
* `searchbase` nom d'une unité organisationnelle
* `seachscope` avec `base` `onelevel` ou `subtree`

```powershell
Get-ADObject -SearchBase "OU=Employees,DC=INLANEFREIGHT,DC=LOCAL" -SearchScope Base -Filter *
```

## Programme powershell maison
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
function LDAPSearch {
    param (
        [string]$LDAPQuery
    )

    $PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
    $DistinguishedName = ([adsi]'').distinguishedName

    $DirectoryEntry = New-Object System.DirectoryServices.DirectoryEntry("LDAP://$PDC/$DistinguishedName")

    $DirectorySearcher = New-Object System.DirectoryServices.DirectorySearcher($DirectoryEntry, $LDAPQuery)

    return $DirectorySearcher.FindAll()

}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
#usagers
LDAPSearch -LDAPQuery "(samAccountType=805306368)"
LDAPsearch -ldapquery "(objectcategory=*)"
LDAPsearch -ldapquery "(&(objectcategory=group)(cn=Sales Department))"
LDAPsearch -ldapquery "(&(objectcategory=user)(cn=michelle))"
foreach ($group in $(LDAPSearch -LDAPQuery "(objectCategory=group)")) {$group.properties | select {$_.cn}, {$_.member}}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Champs utiles
## samAccountType

| ID | Info |
| --- | --- |
| 0 | domain |
| 268435456 | groupes |
| 268435457 | non-security groupes |
| 536870912 | alias |
| 536870913 | non-security alias |
| 805306368 | usagers |
| 805306368 | normal user accounts |
| 805306369 | machine account |
| 805306370 | trust account |
| 1073741824 | app basic group |
| 1073741825 | app query group |

## UserAccountControl flag
| Flag | Nom |
| --- | --- |
| 32 | PASSWD_NOTREQD |
| 64 | PASSWD_CANT_CHANGE |
| 128 | ENCRYPTED_TEXT_PWD_ALLOWED |
| 65536 | DONT_EXPIRE_PASSWORD |
| 262144 | SMARTCARD_REQUIRED |
| 524288 | TRUSTED_FOR_DELEGATION |
| 4194304 | DONT_REQ_PREAUTH |
| 8388608 | PASSWORD_EXPIRED |
| 16777216 | TRUSTED_TO_AUTH_FOR_DELEGATION |

## chemin LDAP pour AD

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
LDAP://HostName[:PortNumber][/DistinguishedName]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* hostname: ordinateur, IP, nom de domaine
	* Utiliser le controlleur de domaine primaire si possible
* port: optionel mais peut être différent de celui par défaut
* distinguished name (DN): nom unique pour un objet
	ex: ```CN=Stephanie,CN=Users,DC=corp,DC=com```
importance de droite à gauche. CN=common Name, DC=Domain Component

# Commandes utiles
- LDAP queries related to AD [computers](https://ldapwiki.com/wiki/Wiki.jsp?page=Active%20Directory%20Computer%20Related%20LDAP%20Query)
- LDAP queries related to AD [users](https://ldapwiki.com/wiki/Wiki.jsp?page=Active%20Directory%20User%20Related%20Searches)
- LDAP queries related to AD [groups](https://ldapwiki.com/wiki/Wiki.jsp?page=Active%20Directory%20Group%20Related%20Searches)
- https://learn.microsoft.com/en-us/archive/technet-wiki/5392.active-directory-ldap-syntax-filters

```powershell
#peut trouver des mots de passe dans les descriptions
(&(objectCategory=user)(description=*))
#Trusted users/computer / unconstrained delegation
(userAccountControl:1.2.840.113556.1.4.803:=524288)
#blank password
(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=32))
#requête récursive des groupes
Get-ADGroup -Filter 'member -RecursiveMatch "CN=Harry Jones,OU=Network Ops,OU=IT,OU=Employees,DC=INLANEFREIGHT,DC=LOCAL"' | select name

Get-ADGroup -LDAPFilter '(member:1.2.840.113556.1.4.1941:=CN=Harry Jones,OU=Network Ops,OU=IT,OU=Employees,DC=INLANEFREIGHT,DC=LOCAL)' |select Name
```
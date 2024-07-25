chemin LDAP pour AD

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
LDAP://HostName[:PortNumber][/DistinguishedName]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* hostname: ordinateur, IP, nom de domaine
Utiliser le controlleur de domaine primaire si possible
* port: optionel mais peut être différent de celui par défaut
* distinguished name (DN): nom unique pour un objet
	ex: ```CN=Stephanie,CN=Users,DC=corp,DC=com```
importance de droite à gauche. CN=common Name, DC=Domain Component


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


# samAccountType

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

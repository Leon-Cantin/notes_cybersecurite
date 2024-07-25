# Pour Windows
## Linux
Attention aux majuscules dans les noms de fichiers!
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#c
x86_64-w64-mingw32-gcc adduser.c -o adduser.exe
#utiliser -l pour ajouter une librairie statiquement
x86_64-w64-mingw32-gcc adduser.c -o adduser.exe -lwinsoc
#c++
x86_64-w64-mingw32-g++ BackupOperatorToDA.cpp -o backupoperatortoda.exe

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
## GLIBC
voir les dépendences
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ objdump -p myprog
...
Version References:
  required from libc.so.6:
    0x09691a75 0x00 02 GLIBC_2.2.5

$ objdump -T myprog | grep realpath
0000000000000000      DF *UND*  0000000000000000  GLIBC_2.2.5 realpath
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Ajouter le flag pour compiler statiquement:
``` -static /usr/lib/x86_64-linux-gnu/libc.a ```
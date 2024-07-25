# Keepass

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
#Trouver le mode de hash cat
hashcat --help | grep -i "KeePass"
#c'est 13400
#Tranformer en format hashcat
keepass2john Database.kdbx > keepass.hash
#attention effacer le début "Database:"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Windows Credential Manager
besoin du mot de passe ou domain admin pour décrypter
Se trouve dans
```shell
C:\Users\[User Profile]\AppData\Roaming\Microsoft\Credentials
#ou
C:\Users\[User Profile]\AppData\Local\Microsoft\Credentials
C:\Windows\system32\config\systemprofile\AppData\Local\Microsoft\Credentials #(Windows 8 and later)
C:\Documents and Settings\[User Profile]\Application Data\Microsoft\Credentials #(Windows XP)
C:\Documents and Settings\[User Profile]\Local Settings\Application Data\Microsoft\Credentials #(Windows XP)
```

Et protégé par une clef qui se trouve généralement:

	C:\Users\$USER\AppData\Roaming\Microsoft\Protect\$SUID\$GUID

https://github.com/gentilkiwi/mimikatz/wiki/howto-~-credential-manager-saved-credentials
https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/dpapi-extracting-passwords
https://www.thehacker.recipes/ad/movement/credentials/dumping/dpapi-protected-secrets
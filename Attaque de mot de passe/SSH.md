
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
#Changer permissions
chmode 600 id_rsa
#transformer en hash (aussi supprimer le nom du fichier au début)
ssh2john id_rsa > hashfile
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
sudo nmap --script ssh-brute --script-args users 192.168.203.122
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/[user]/.ssh/id_rsa
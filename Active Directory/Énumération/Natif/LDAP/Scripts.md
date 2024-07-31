## [ldapsearch](https://linux.die.net/man/1/ldapsearch)
Outil Natif linux
```shell
ldapsearch -H ldap://10.129.1.207 -x -b "dc=inlanefreight,dc=local" -s sub "(&(objectclass=user))"
```

## ldapsearch-ad
```shell
python3 ldapsearch-ad.py -l 10.129.1.207 -d inlanefreight -u james.cross -p Summer2020 -t pass-pols
```

| commande | Description |
| --- | --- |
| `-t pass-pols` |  politique de mot de passe |

## windapsearch
```shell
python3 windapsearch.py --dc-ip 10.129.1.207 -u inlanefreight\\james.cross --da
```
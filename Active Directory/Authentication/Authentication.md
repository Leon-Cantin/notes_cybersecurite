# Cache
Mots de passe gardé pour renouvler TGT gardé dans Local Security Authority Subsystem Service (LSASS). Ce processus roule en tant que SYSTEM. Doit être SYSTEM ou administrateur locale.
Format non documenté et encrypté avec une clef LSASS-stored.

## Clef non exportable
Peut utiliser le module crypto de mimikatz. crypto::capi, crypto::cng

## Domain Cached Credentials (MSCache2)
l'hôte garde en mémoire les 10 derniers hashes qui se sont authentifiés en cas de panne de communication avec le DC. 
* doit être admin
* `HKEY_LOCAL_MACHINE\SECURITY\Cache`
* Presqu'impossible à casser
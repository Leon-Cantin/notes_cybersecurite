## Microsoft Defender (Windows Defender)
`Get-MpComputerStatus` si `RealTimeProtectionEnabled` est vrai alors il est actif.
```powershell
#cmd
sc query windefend
```

## Windows Firewall
```powershell
#powershell
netsh advfirewall show allprofiles
```

## AppLocker
Permet de bloquer certaines applications. Souvent powershell.
```powershell-session
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```
## PowerShell Constrained Language Mode
Bloque certaines fonctionnalités de powershell comme COM objects. Permet .NET et XAML
```powershell-session
$ExecutionContext.SessionState.LanguageMode
```
## LAPS (Local Administrator Password Solution)
Fait une rotation des mots de passe admin locale
[LAPSToolkit](https://github.com/leoloobeek/LAPSToolkit)
```powershell
#Cherche les groupes qui peuvent lire
Find-LAPSDelegatedGroups
#Cherche les usagers qui peuvent lire
Find-AdmPwdExtendedRights
#Obtenir mot de passe
Get-LAPSComputers
```
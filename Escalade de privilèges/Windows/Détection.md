## Event Log Readers
Administrators ou membres de Event Log Readers peuvent lire le log.
### évènement intéressant
| **ID** | **Évènement** | **Description** |
| --- | --- | --- |
| 4688 | A new process has been created | |

### [wevtutil](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/wevtutil)
```powershell-session
wevtutil qe Security /rd:true /f:text | Select-String "/user"
wevtutil qe Security /rd:true /f:text /r:share01 /u:julie.clay /p:Welcome1 | findstr "/user"
```

### [Get-WinEvent](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-7.1)
`-Credential`  Pour utiliser un autre compte
```powershell
Get-WinEvent -LogName security | where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*'} | Select-Object @{name='CommandLine';expression={ $_.Properties[8].Value }}
```

## [PowerShell Operational](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_logging_windows?view=powershell-7.1)
``` powershell
$psrl = Get-Module PSReadLine
$psrl.LogPipelineExecutionDetails = $true
Get-Module PSReadline | Select-Object Name, LogPipelineExecutionDetails
```
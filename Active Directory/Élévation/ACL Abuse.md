Certaines permissions peuvent êtres abusés avec PowerView ou outils natifs.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~powershell
#powerview
Get-ObjectAcl -Identity stephanie
Get-ObjectAcl -Identity "Management Department" | ? {$_.ActiveDirectoryRights -eq "GenericAll"} | select SecurityIdentifier,ActiveDirectoryRights
#Convertir en nom lisible:
Convert-SidToName S-1-5-21-1987370270-658905905-1781884369-1104
"S-1-5-21-1987370270-658905905-1781884369-512","S-1-5-21-1987370270-658905905-1781884369-1104","S-1-5-32-548","S-1-5-18","S-1-5-21-1987370270-658905905-1781884369-519" | Convert-SidToName
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

| *Permission* | *Description* | *Commande PowerView* | *Commande autre* |
| --- | --- | --- | --- |
|`ForceChangePassword`| Password change for object | `Set-DomainUserPassword`| |
|`Add Members`| Add member to group |`Add-DomainGroupMember`| `net group <group> <user> /add /domain` |
|`GenericAll`| Full permissions on object |`Set-DomainUserPassword` ou `Add-DomainGroupMember`|
|`GenericWrite`| Edit certain attributes on the object |`Set-DomainObject`|
|`WriteOwner`| Change ownership of the object |`Set-DomainObjectOwner`|
|`WriteDACL`| Edit ACE's applied to object |`Add-DomainObjectACL`|
|`AllExtendedRights`| Change password, reset password, etc. |`Set-DomainUserPassword` ou `Add-DomainGroupMember`|
|`Addself`|  Add ourselves to for example a group |`Add-DomainGroupMember`|

![[ACL_attacks_graphic.webp]]
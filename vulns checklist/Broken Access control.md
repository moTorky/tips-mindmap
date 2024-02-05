most of vulns caused by send injections in requests, Broken access control occurs when sensitive resources or functionality are not properly protected.
Hunting for these vulnerabilities doesn’t involve tampering with code or crafting malicious inputs; instead, it requires creative thinking and a willingness to experiment
##### examples
- first and most found is [[IDOR]]
- Exposed Admin Panels
	- scan for open ports , cookie tampering
	- use google dorks, URL brute-forcing
	- using [[SSRF]]
- Directory Traversal Vulnerabilities
	- happen when attackers can view, modify, or execute files they shouldn’t have access to by manipulating filepaths in user-input fields
### hunt for it
1. Learn About Your Target
	1. Browse the application as a regular user
	2. read the application’s engineering blogs and documentation
	3. The more you understand about the architecture, development process, and business needs of that application, the better you will be at spotting these vulnerabilities
2. Intercept Requests While Browsing, pay attention to sensitive functionalities
3. Think Outside the Box
	> Play with the requests that you have intercepted and craft requests that should not be granted.
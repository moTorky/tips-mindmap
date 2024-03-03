### passive recon
> obtaining information about a target without directly interacting with the target’s devices

open-source intelligence (OSINT), which is data collected from publicly available sources. You will be on the hunt for API endpoints, credential information, version informa- tion, API documentation, and information about the API’s business pur- pose. 
**Any discovered API endpoints will become your targets later, during active recon**

discovering the API’s business purpose can provide you with insight about potential business logic flaws.

**build a profile of the target’s attack surface.**

Start by casting a wide net using an array of tools to collect data. Then perform more tailored searches based on the collected data to obtain more Discovering APIs refined information. Repeat this process until you’ve mapped out the target’s attack surface.

#### Phase 
1.  Cast a Wide Net: find general information about the API such as its usage, design and architecture, documentation, and business purpose, as well as industry-related. use `DNS Dumpster`and `Amass` to show all the hosts related to the target’s domain name
2. Adapt and Focus: by combining the information gathered from separate tools to gain new insights
3. Document the Attack Surface: Create a task list of the passive reconnaissance findings that could prove useful throughout the rest of the attack.
#### tools
1. **Google Hacking**: reveal all sorts of public API-related information about your target, including vulnerabilities, API keys, and usernames> 
   `inurl:"/wp-json/wp/v2/users"`
   `intitle:"index.of" intext:"api.txt"`
   `inurl:"/includes/api/" intext:"index of /"`
   `ext:php inurl:"api.php?action="`
   `intitle:"index of" api_key OR "api key" OR apiKey -pool`
   `company_name developer docs`
   `company_name inurl:swagger`
2. **Shodan**: to discover external-facing APIs and get information about your target’s open ports> 
   `hostname:"targetname.com"` ,`"content-type: application/json"`  ,use shodan browser extensions
3. get info about API using https://apis.guru/
4. **OWASP Amass**: 
	1. active option, Amass will collect information directly from the target by requesting its certificate information
	2. passive collects data from search engines (such as Google, Bing, and HackerOne), SSL certificate sources (such as GoogleCT, Censys, and FacebookCT), search APIs (such as Shodan, AlienVault, Cloudflare, and GitHub), and the web archive Wayback: 
	   `amass enum -passive -d twitter.com |grep api`
	commands 
	   - `intel` command to collect SSL certificates, search reverse Whois records, and find ASN IDs associated with your target: `amass intel -addr <target IP addresses> >domain.txt`
	   - `amass intel -d <target domain> –whois` could give you a ton of results. Focus on your target organization.
	   - `amass enum -passive -d <target domain>` passive subdomain 
	   - `amass enum -active -d <target domain>` which try attempt DNS zone transfers, and grab SSL certificate info
	   - brute-force subdomains: `amass enum -active -brute -w wordlists/API_superlist -d <target domain> -dir [directory name]`
	   - `amass viz -enum -d3 -dir <directory name>`: visualize relationships between the data Amass returns, and generate HTML web page
1. **Github**: could reveal your target’s API capabilities, documentation, and secrets, such as admin-level API keys, passwords, and tokens.
	1. search for target name with words like "api key,” "api keys", "apikey", "authorization: Bearer", "access_token", "secret", or “token.” , use `Pastehunter` tool to find exposed sensitive information
	2. Code scan to find endpoints, find vulns/changes on old commits
	3. find software bugs in the Issues tab
		*If an issue is open, there is a good chance that the vulnerability is still live within the code*
		*note the date of the issue and then search the commit history for any changes around that time*
	1. review proposed changes in the Pull requests tab
	>if you don’t find weaknesses in a GitHub repository, use it instead to develop your profile of your target. Take note of programming languages in use, API endpoint information, and usage documentation, all of which will prove useful moving forward.
1. **TruffleHog** => `sudo docker run -it -v "$PWD:/pwd" trufflesecurity/trufflehog:latest github --org=target-name`
2. **Wayback Machine** for 
	1. find endpoints, 
	2. old version's documentation
	3. Zombie APIs which are endpoints that still exist even though the API provider believes them to be retired 
	4. Finding and comparing historical snapshots of API documentation can simplify testing for Improper Assets Management
### active recon
this process is time-efficient because it keeps you engaging with the target while automated scans are running in the background. Whenever you’ve hit a dead end in your analysis, return to your automated scans to check for new findings. 
The process is not linear: after each phase of increasingly targeted scanning, you’ll analyze the results and then use your findings for further scanning. At any point, you might find a vulnerability and attempt to exploit it. If you successfully exploit the vulnerability, you can move on to post-exploitation. If you don’t, you return to your scans and analysis
##### Phases
1. Opportunistic Exploitation: If you discover a vulnerability at any point in the active recon process, you should take the opportunity to attempt exploitation. With experience, you’ll learn when to avoid get- ting lost in a potential rabbit hole 
2. Detection Scanning: is a background phase, so while your scanner scan for open-ports, services, OS types. u should do the hands-on
3. Hands-on Analysis: learn about all the potential levers you can interact with and test them out
	1.  examine the web page, intercept requests, look for API links and documentation, and develop an understanding of the business logic involved
	2.  consider the application from three perspectives: guests, authenticated users, and site administrators
		1. Guest How would a new user use this site? Can new users interact with the API? Is API documentation public? What actions can this group perform? 
		2. Authenticated User What can you do when authenticated that you couldn’t do as a guest? Can you upload files? Can you explore new sections of the web application? Can you use the API? How does the web application recognize that a user is authenticated? 
		3. Administrator Where would site administrators log in to manage the web app? What is in the page source? What comments have been left around various pages? What programming languages are in use? What sections of the website are under development or experimental?
	3.  intercepting the HTTP traffic with Burp Suite
	4. When you run into roadblocks, it’s time to review new results from the phase one scans running in the background and kick off phase three: targeted scans.
4. Targeted Scanning hould focus on the specific type of API, its version, the web appli- cation type, any service versions discovered, whether the app is on HTTP or HTTPS, any active TCP ports
	1. you should know the URLs of the web application and can begin brute-forcing uniform resource identifiers to find hidden directories and files
	2. Once these tools are up and running, review results as they flow in to perform a more targeted hands-on analysis.
#### tools
1. Baseline Scanning with Nmap, save open ports to file `-oN, -oX, -oG`, compare new scan with last one
3. Finding Sensitive Information with Chrome DevTools
	1. Open in Sources Panel to review JS code, search for words "api", "api-key", "apikey", "password", "secret",  use JS tools to find it for u  
	2. DevTools Memory tab, select the target and click "take snapshot". in the created snapshot file use `Ctrl+F` to search for words to find apis  
	3. DevTools Performance tab to record certain actions (such as clicking a button) and review them over a timeline broken down into milliseconds.
4. Validating APIs with Burp Suite
5. Crawling URIs with OWASP ZAP 
	1. **we can use Burp as proxy for ZAP/fuff to collect all endpoints in one**
	2. lick the Quick Start tab, Enter the target URL and click Attack
	3. Navigate to the Quick Start tab and select Manual Explore which make ZAP work in the background to automatically scan for vulnerabilities.
6. Brute-Forcing URIs with Gobuster: 
	works well for a quick scan of a web application to discover URL paths,
	2. Finding Hidden Paths in Robots.txt
	3. `gobuster dir -u http://target.com -w wordlist  -x 200,202,301 -b 302` 
	   wordlist from [Assetnote API wordlists](http://wordlists.assetnote.io), https://gist.github.com/yassineaboukir/8e12adefbd505ef704674ad6ad48743d/
7. Discovering API Content with Kiterunner
	Kiterunner will not only use all HTTP request methods common with APIs (GET, POST, PUT, and DELETE) but also mimic common API path structures.
	`kr scan http://192.168.1.2:8090 -w ~/api/wordlists/data/kiterunner/routes-large.kite`
	`kr brute <target> -w ~/api/wordlists/data/automated/nameofwordlist.txt`
	use `kr` response to interact with the endpoint using `kp replay`:
	`kr kb replay "GET 414 [ 183, 7, 8] http://192.168.50.35:8888/api/privatisations/ count 0cf6841b1e7ac8badc6e237ab300a90ca873d571" -w ~/api/wordlists/data/kiterunner/routes- large.kite`
### AUTOMATE ABOVE 




==$devmrt's browser segfult apikey: 8lgm-12DyRTFOPBBLoBvUm7edX4nE==
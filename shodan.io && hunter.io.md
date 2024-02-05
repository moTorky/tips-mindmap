- anonymous ftp 
	hostname:*.[target.com](http://target.com/) "220" "230 Login successful." port:21  
	OR  
	hostname:*.[target.com](http://target.com/) "Login ok" port:21
- [awesome-shodan-queries](https://github.com/jakejarvis/awesome-shodan-queries)  
- dorks
```bash
org:"http://target.com" 
http.status:"<status_code>" 
product:"<Product_Name>" 
port:<Port_Number> “Service_Message” 
port:<Port_Number> “Service_Name” 
http.component:"<Component_Name>" 
http.component_category:"<Component_Category> 
http.waf:"<firewall_name>" 
http.html:"<Name>" 
http.title:"<Title_Name>" 
ssl.alpn:"<Protocol>" 
http.favicon.hash:"<Favicon_Hash>" get hash using `favscan <URL>` 
net:"<Net_Range>" (for e.g. 104.16.100.52/32) 
http://ssl.cert.subject.cn:"<http://Domain .com>" 
asn:"<ASnumber>" 
hostname:"<hosthame>" 
ip:"<IP_Address>" 
all:"<Keyword>" 
“Set-Cookie: phpMyAdmin” 
“Set-Cookie: lang=" 
“Set-Cookie: PHPSESSID" 
“Set-Cookie: webvpn” 
“Set-Cookie:webvpnlogin=1" 
“Set-Cookie:webvpnLang=en” 
“Set-Cookie: mongo-express=" 
“Set-Cookie: user_id=" 
“Set-Cookie: phpMyAdmin=" 
“Set-Cookie: _gitlab_session” 
“X-elastic-product: Elasticsearch” 
“x-drupal-cache” 
“access-control-allow-origin” 
“WWW-Authenticate”  
“X-Magento-Cache-Debug”   
“kbn-name: kibana”
```

- hunt for fresh CVEs using `http.favicon.hash` of the framework 
	- `shodan download Juniper 'http.favicon.hash:2141724739 org:"AT&T Services Inc."'`
	- `shodan parse Juniper.json.gz --fields ip_str,port --separator " " | awk '{print $1":"$2}' | anew Juniper_ips`
	- run your tool on ip lists
	- example: https://medium.com/@asbawy/automation-hacks-unearthing-a-critical-rce-the-easy-way-ad64f01a06a3
	- also u can use `interactsh-server` command line to get server listener 
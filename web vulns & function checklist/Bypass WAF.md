try to hunt behind the WAF be find the original IP for the site, use shodan, hunter.dev to find it 

> [!NOTE] techniqe
> you can try; many more exist. For example, you can hex-encode, URL-encode, double-URL-encode, and vary the cases (uppercase or lowercase characters) of your payloads. You can also try to insert special characters such as null bytes, newline characters, escape characters (\), and other special or non-ASCII characters into the payload. Then, observe which payloads are blocked and which ones succeed, and craft exploits that will bypass the filter to accomplish your desired results

#### WAF threshold categories 
- Too many requests for resources that do not exist 
- Too many requests within a small amount of time 
- Common attack attempts such as SQL injection and XSS attacks 
- Abnormal behavior such as tests for authorization vulnerabilities
if the WAF threshold is 3-requests, then in your forth request WAF gonna send you a warning, alerting other defenders, monitoring your activity with more scrutiny, or block your
###### but how WAF determine who you are?
since HTTP is stateless protocol, WAF use your request's info, This information commonly includes your IP address, origin headers, authorization tokens, and metadata. Metadata is information extrapolated by the API defenders, such as patterns of requests, the rate of request, and the combination of the headers included in requests
more advanced products could block you based on pattern recognition and anomalous behavior, using baseline. this products can block customers by mistake.

#### how to Detect if WAF in place
tow approaches can be followed:
- attack-first, ask-questions-later approach; where u send malicious requests first then figure out if any WAF in place->   may you get blocked 
- use app as it was intended, and look for evidence such `X-CDN` header 
```header
X-CDN: Imperva 
X-CDN: Served-By-Zenedge 
X-CDN: fastly 
X-CDN: akamai 
X-CDN: Incapsula 
X-Kong-Proxy-Latency: 123 
Server: Zenedge 
Server: Kestrel 
X-Zen-Fury 
X-Original-URI
```
- tools `W3af`, `Wafw00f`, nmap script: `nmap -p 80 –script http-waf-detect <url>`
> in this step make register some accounts for furzer usage,  make sure to use different names and email addresses for each one, or even use VPN or proxy while you register for an account. 
#### basic WAF bypass
**is a process of trial and error**; u gonna need your registered accounts:
1. first you need identify actions that will trigger a response 
2. then attempt to avoid those actions or bypass detection using other account and following techniques: 
- String Terminators: placed in different parts of the request's payload. 
	- Wfuzz bad characters list
	- SecLists’ metacharacters list
	```list
	%00 0x00 // ; % ! ? [] %5B%5D %09 %0a %0b %0c %0e
	```
- random Case Switching
- Encoding Payloads: urlenocde, base64, HTML
	- ocus on the characters that may be blocked: `< > ( ) [ ] { } ; ' / \ |` 
- paly with HTTP headers: 
	- change `Content-encodeing: random_text`

#### tools
>Once you’ve discovered a successful method of bypassing a WAF, it’s time to leverage the functionality built into your fuzzing tools to automate your evasive attack
##### burp intruder rules ![[Pasted image 20240213142950.png]]
- Add Prefix rule ->before payload.  Add Suffix rule ->after payload. 
- u can add rules must as want 

##### `Wfuzz`
`wfuzz` have many encoder all listed in [wfuzz documention](https://wfuzz.readthedocs.io) , or `wfuzz -e encoders` 
most common: `urlencode`,`base64`, `md5`,`random_upper`,`hexlify` 
examples: 
- base64 encode before sending:
   `wfuzz -z file,wordlist/api/common.txt,base64 http://hapihacker.com/FUZZ`
- use 3 encoder on at time:
  `wfuzz -z list,a-b-c,base64-md5-none -u http://hapihacker.com/api/v2/FUZZ`
   - use 2 sequence encoders:
 `wfuzz -z list,aa-bb-cc,base64@random_upper -u http://localhost:8888/identity/ api/auth/FUZZ`

#### github repose
- [wesome-WAF GitHub repo](https://github.com/0xInfection/Awesome-WAF)
- Without limiting the number of requests consumers can make, an API provider’s infrastructure could be overwhelmed by the requests.
- requests without enough resources will lead to the provider’s systems crashing, (DoS) state
- some rate-limit doesn't need to be passed; say a rate limit is set to 15,000 requests per minute and you want to brute-force a password with 150,000. just make sure your fuzzer not pass that rate:
	- `wfuzz`: use `-t` to specify the concurrent number of connections,`-s` to specify a time delay between requests in seconds; *`-s 0.1` => 10per sec*.
	- burp intruder Resource Pool: in millisecond ; *1 mill => 1000per sec* 
- Before you move on to bypassing rate limiting, determine if consumers face any consequences for exceeding a rate limit
#### test for it
1. you can do so by sending a barrage of requests to the API. If rate limiting is functioning, 
	1. you should receive response informing you that you’re no longer able to make requests, 
	2. or HTTP 429 status code. 
	3. headers `x-rate-limit:`, `x-rate-limit-remaining:`, `Retry-After:`
2. Once you are restricted from making additional requests, it’s time to attempt to see how rate limiting is enforced. Can you bypass it by 
	1. modify path, adding or removing a parameter: 
		1. `POST /api/myprofile%00`, `POST /api/myprofile%20`, use upper-lower case
		2. add random parameters: `POST /api/myprofile?test=§1§` 
	2. Origin Header Spoofing:
		origin request headers tell the web server where a request came from
		```
		X-Forwarded-For 
		X-Forwarded-Host 
		X-Host 
		X-Originating-IP 
		X-Remote-IP 
		X-Client-IP
		X-Remote-Addr
		```
		- including private IP addresses, local-host IP (127.0.0.1), or an IP address relevant to your target discovered from recon
		- if you include all headers at once, you may receive a 431 Request Header Fields Too Large, then send fewer headers per request until you succeed. 
	1. User-Agent [seclists/Fuzzing/User-Agents](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/User-Agents/UserAgents.fuzz.txt)
	2. altering your IP address by use IP Rotate Burp Suite Community extension 
	   **There is a small cost associated with using the AWS API gateway**

>You’ll know you’ve succeeded if an x-rate-limit header resets or if you’re able to make successful requests after being blocked.
is an any weakness within the API authentication process, we know that REST APIs is stateless, so APIs often require users to undergo a registration process in order to obtain a unique token that  sent within requests to demonstrate that they’re authorized to make such requests.
##### techniques used to test for it
1. *Password Brute-Force Attacks*: make a custom list using [cupp](https://github.com/Mebus/cupp), or other lists [mentalist](https://github.com/sc0tfree/mentalist) 
	-  `wfuzz -d '{"email":"a@email.com","password":"FUZZ"}' --hc 405 -H 'Content-Type: application/ json' -z file,/home/hapihacker/rockyou.txt http://192.168.195.130:8888/api/v2/auth`
	- `wfuzz -u vulnexample.com/api/v2/user/dashboard –hc 404 -H "token: Ab4dt0k3nFUZZFUZ2ZFUZ3Z1" -z list,a-b-c-d -z list,a-b-c-d -z range,0-9` note: `FUZZ`, `FUZ2Z`, `FUZ3Z1` is fuzzy places 
	- burp intruder 
1. *Password Reset and MFA-OPT Brute-Force Attacks*
	- check other API version `/v1`, `/v2`
3. *Password Spraying* use a small password list(size=$account looked trial$-1), and valid usernames
	1. burp $cluster bumb$ attack mode
4. *weak token generation* process by collect a sampling of tokens and analyze them for similarities, if it doesn’t rely on a high level of randomness, or entropy, there is a chance we’ll be able to create our own token or hijack someone else’s.
	1. use **burp Sequencer** , if could Brute-Forcing Predictable Tokens
		1. Live Token Capture Analysis: if u can access token generator endpoint
		2. **Manual Load Analysis** when u have list of tokens 
5. *Token handling* is the method of transmitting tokens across a network, or the presence of hardcoded tokens which exposed in JavaScript, GitHub repos
7. access sensitive resources without being authenticated
8. [[JWT attacks]], tools: `jwt_tool`, or `custom python script` , `burp Bapp`
> The secret to successfully discovering most API vulnerabilities is knowing where to fuzz and what to fuzz with
> **fuzzing tools:**
> Wfuzz, Burp Suite Intruder, and Postman’s Collection Runner
> **fuzz strategies:**
> - fuzzing wide and fuzzing deep
> - find the accepted HTTP methods, fuzzing wide and fuzzing deep.

- fuzzing as the process of sending requests with various types of input to an endpoint in order to provoke an unintended result. 
- use a *Generic fuzzing payloads* to trigger errors, then use *Targeted fuzzing payloads* for exploitations, Targeted fuzzing payload types might include API object or variable names, XSS payloads, directories, file extensions, HTTP request methods, JSON or XML data, SQL or No SQL commands, OS commands 
```Generic_payloads
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA 
9999999999999999999999999999999999999999 
~'!@#$%^&*()-_+ 
{}[]|\:''; 
'<>?,./ 
%00 
0x00 
$ne 
%24ne 
$gt 
%24gt 
|whoami 
-- - 
' '' ' 
OR 1=1-- - 
'' '''''' 
漢, さ, Ж, Ѫ, Ѭ, Ѧ, Ѩ, Ѯ 
😀 😃 😄 😁 😆
```
- payloads: [SecLists](https://github.com/danielmiessler/SecLists) fuzz payloads, [fuzzdb](https://github.com/fuzzdb-project/fuzzdb), `All_attack.txt` from [wfuzz](https://github.com/xmendez/wfuzz) ,[List of Naughty Strings](https://github.com/minimaxir/big-list-of-naughty-strings/), or random strings using `for` loop and `dev/random` 
- From the filtered response, you can deduce that the developers implemented an error handling. 
- response filtered based on size, response code. use Burp Comparer to find differences  
#### fuzzing strategies
- **Fuzzing wide** 
	- is the act of sending an input across all of an API’s unique requests in an attempt to discover a vulnerability. 
	- test for issues across all unique requests, injection, and other information disclosures
	- fuzz wide can used to test for improper assets management
- **Fuzzing deep**
	- is the act of thoroughly testing an individual request with a variety of inputs, replacing headers, parameters, query strings, endpoint paths, and the body of the request with your payloads
	- Fuzzing deep is best used for testing many aspects of individual requests.
	- fuzz deep can used to test for BOLA, BFLA, injection, and mass assignment
#### fuzzing tools
###### Fuzzing Wide with Postman
-  Collection Runner makes it easy to run tests against all API requests
- set a variable to a fuzzing payload entry and test it across all
- Send a single payload across the API and check for anomalies. by select the fuzzing environment and then use the variable shortcut `{{variable name}}` like `{{FUZZ1}}`
- fuzz places include headers, part of url, any custom variables
- use **Find and Replace** to search a collection (or all collections) and replace certain terms with a replacement of your choice such replace `<email>` with `{{FUZZ1}}`
- create a simple test in the Tests panel to help you detect anomalies, to test only 200 use
```java
pm.test("Status code is 200", function () { 
 pm.response.to.have.status(200); 
});
```
- run collection runner from "Runner Overview", "run" buttons 
- to make a baseline uncheck "Keep Variable Values" form first run, then check it when fuzzing, then compare
###### Fuzzing Deep with Burp Suite
- fuzz deep whenever you want to drill down into specific requests.
- use Intruder to fuzz every header, parameter, query string, and endpoint path, along with any item included in the body of the request.
-  perform a deep fuzz that passes hundreds or even thousands of fuzzing inputs into each value to see how the API responds.
- When you’re fuzzing, it is always worthwhile to request the unexpected. 
	- If a field expects an email, send numbers. If it expects numbers, send a string. If it expects a small string, send a huge string. If it expects a Boolean value (true/false), send anything else. 
- Another useful tip is to send the expected value and include a fuzzing attempt following that value. For example, email fields make a “not a valid email.” error, u can use `"user": "hapi@hacker.com§test§`
- A sniper attack will cycle a single wordlist through each attack position, which helps to find out which place trigger the error
###### Fuzzing Deep with `Wfuzz`
-  Burp Suite CE, Intruder will limit the rate you can send requests, so you should use `Wfuzz` 
- make the attack got throw burp using `-p ip:port`
```bash
$ wfuzz -z file,/home/hapihacker/big-list-of-naughty-strings.txt -p 127.0.0.1:8080:HTTP -H "Content-Type: application/ json" -H "x-access-token: [...]" --hc 400 -X PUT -d "{ \"user\": \"FUZZ\", \"pass\": \"FUZZ\", \"id\": \"FUZZ\", \"name\": \"FUZZ\", \"is_admin\": \"FUZZ\", \"account_balance\": \"FUZZ\" }" -u http://192.168.195.132:8090/api/user/edit_info
```
- Brute-Forcing Basic Authentication:
     `wfuzz -w usernames.txt -w passwords.txt --basic FUZZ:FUZ2Z http://example.com/admin`
- XSS fuzzer, `wfuzz` can search for fuzz input in the content response:
     `wfuzz -w xss.txt --filter "content~FUZZ" http://example.com/get_user?user_id=FUZZ `
- 
#### discover vulns by fuzzing
###### Fuzzing Wide for Improper Assets Management
- change `baseURL` variable from `https://petstore.swagger.io/v2` use keywords, like `v1`, `v3`, `test`, `mobile`, `uat`, `dev`, and `old`, as well as any interesting paths discovered during analysis or reconnaissance
- add some words to the pass `/internal/` => `/api/v2/internal/users`, `/api/internal/v2/users`
-  If you discover an improper asset management vulnerability, test the discovered endpoint for other vulns such BOLA, BFLA
- review GitHub changelog, if `v2/account` vuln to BOLA, then check `v2/account` too
###### Testing Request Methods 
- Wfuzz
	`wfuzz -z list,GET-HEAD-POST-PUT-PATCH-TRACE-OPTIONS-CONNECT -X FUZZ http://testsite.com/api/v2/account` NOTE the response code, size 
###### Fuzzing “Deeper” to Bypass Input Sanitization
- trick the server’s input sanitization code into processing a payload it should normally restrict
	1. try sending something that takes the form of the restricted field `"user": "a@b.com%00§test§"`
	2. Instead of a null byte, try sending a pipe (|), quotes, spaces, and other escape symbols `"user": "a@b.com§escape§§test§"`
	3. use Burp Suite’s cluster bomb attack mode
###### Fuzzing for Directory Traversal
ou could leverage a series of path traversal dots and slashes in place of the escape symbols described in the previous section, like the following ones:
```payloads
.. 
..\ 
../ 
\..\ 
\..\.\
```

vulnerability where a user of one role or group is able to access the API functionality of another role or group. either functions of a similarly privileged group, or it could be a privilege escalation.
- BOLA is about unauthorized access, whereas BFLA is about unauthorized actions.
> An API won’t always use administrative endpoints for administrative functionality. Instead, the functionality could be based on HTTP request methods such as GET, POST, PUT, and DELETE. If a provider doesn’t restrict the HTTP methods a consumer can use, simply making an unauthorized request with a different method could indicate a BFLA vulnerability.
> **Sometimes an API provider has secured one request method from unauthorized requests but has overlooked another.**
#### test for it
- find administrative API documentation/reverse engineer/fuzz and send requests as an unprivileged user or USERA, If access controls are in place, you’ll likely receive an HTTP 401 Unauthorized or 403 Forbidden response, if not you have discovered a BFLA vulnerability
- second way is make new client and test every action using his API-key/token
- avoid testing for DELETE while fuzzing, unless you’re targeting a test environment.
#### automation test
##### postman
1. BFLA test
	1. make baseline by send normal requests, using UserA token, UserA_id in **collection variables** 
	2. change environment vars from userA data(id,token) to userB
	3. send collection requests, and note the responses
2. privilege escalation test
	1. make an administrator collection by (find admin documentation, reverse eng, fuzz)
	2. run the collection as a low-privileged user by change the **collection variables** 
	3. if u make the collection by add requests, try to use all request methods on every recourse
##### burp suite find and replace automate test for it
burp suite history tab is super powerful when u not have an api documentation, cuz it collect your requests while u interact with the api, after collecting your requests as UserA use the *Match and Replace* option to perform a large-scale replacement of a variable like an authorization token.
- match and replace the authorization headers with UserB’s and repeat the requests

##### if u discover BFLA try to test other vulns on behave the other user, such mass assignment(disable MFA, change his email,..)  or account take over ATO using self XSS


- like web Broken Access Control vuln
- occur when an API provider allows an API consumer access to resources they are not authorized to access. ex UserA/un-authUser able to successfully request UserB’s resources.
- how to identify resource IDs, use A-B and A-B-A testing, and speed up your testing with Postman and Burp Suite.
##### techniques used to test for it
- you can test for BOLAs by understanding how an API’s resources are structured and attempting to access resources you shouldn’t be able to access.
```php
if u found this                           then u can test
GET /api/resource/1                       GET /api/resource/3 
GET /user/account/find?user_id=15         GET /user/account/find?user_id=23 
POST /company/account/Apple/balance       POST /company/account/Google/balance 
POST /admin/pwreset/account/90            POST /admin/pwreset/account/111
 **Attempt to use all possible methods to interact with resources**
```
- **Locating Resource IDs**
	-  Look for user ID names or numbers, resource ID names or numbers, organization ID names or numbers, emails, phone numbers, addresses, tokens, or encoded payloads used in requests to retrieve resources. examples
	> Table 10-1: [[Hacking APIs - Early Access.pdf#page=250&selection=56,0,57,1|Hacking APIs - Early Access, page 250]]
	- you could simply brute-force all account numbers within a certain range and filter successful response.
	- *ID compo*: `GET /api/v1/UserA/data/2222` , `GET /api/v1/UserB/data/3333` Sometimes, just requesting the resource won’t be enough; instead, you’ll need to request the resource as it was meant to be requested, often by supplying both the resource ID and the user’s ID
	- use *Nested objects* => `{"Account": 2222 }` , `{"Account": {"Account": 3333 } }`
- **A-B Testing for BOLA**
	1. create tow accounts, 
		- create resource using USERA
		- Using UserB’s token, make the request for UserA’s resources
		>the scale of this testing is small, but if you can access one user’s resources, you could likely access all user resources of the same privilege level
	2. create three accounts for testing. That way, you can create resources in each of the three different accounts, **detect any patterns in the resource identifiers**
	1. Create multiple accounts at each privilege level to which you have access, Use all the methods at your disposal.
- **Side-Channel disclosure BOLA**
	- this is any information gleaned from unexpected sources, such as timing data, response codes and lengths to determine if resources exist. `X-Response-Time` very helpful to find change in timing data
	- example, if an API responds to nonexistent resources with a 404 Not Found but has a different response for existing resources, such as 405 Unauthorized
	- you’ll be able to perform a BOLA side-channel attack to discover existing resources such as usernames, account IDs, and phone numbers. which can used to 
		- perform brute-force attacks to gain entry to valid accounts.
		- perform other BOLA tests, such as the *ID combo* BOLA test 
#### automation test
same methods from [[Broken Function level authorization (BFLA)]]
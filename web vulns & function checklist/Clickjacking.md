### what && how
> Clickjacking, or user-interface redressing, is an attack that tricks users into clicking a malicious button that has been made to look legitimate.
>Attackers achieve this by using HTML page-overlay techniques to hide one web page within another using `<iframe>` tag, so if the page can’t be framed, this attack not succussed 

> rarely considered in scope for bug bounty, cuz this attack need lot of user interaction
### simple POC
> note `opacity`, `z-index` for `victim-site`, and `position`, `z-index` for decoy
```html
<html>
 <style>
 #victim-site {
	 width:500px;
	 height:500px;
	 opacity:0.00001; /*make it 0 in real poc*/
	 z-index:1; /*make it float in top*/
 } 
 #decoy { 
	 position:absolute; /* to make the site overlab */
	 width:500px; 
	 height:500px;  
	 z-index:-1; /*always in back*/
 }
 </style>
<div id="decoy">
	<h3> Welcome to my site! </h3>
	<h3> This is a cybersecurity newsletter that focuses on bug bounty news Please subscribe to receive new cybersecurity articles in your email!</h3>
	<form action="/subscribe" method="post">
		<label for="email">Email</lable>
		<input type="text" id="email" value="Please enter your email!">
		<input type="submit" value="Submit">
	</form>
 </div>
 <iframe id="victim-site"
 src="https://www.example.com/transfer_money?
 recipient=attacker_account_12345&amount=5000"
 width="500" height="500">
 </iframe>
</html>
```
![[Pasted image 20240123142644.png]]The Transfer Balance button lies directly on top of the Subscribe button. Victims think they’re subscribing to a newsletter, but they’re actually clicking the button to authorize a balance transfer.
### prevention
Two conditions must be met for a clickjacking vulnerability to happen.
1. the vulnerable page has to have **functionality that executes a state-changing action** on the user’s behalf: (changing the user’s account settings or personal data)
2. **the vulnerable page has to allow itself to be framed by an iframe on another site**
	- `X-Frame-Options` :
		if the HTTP response headers have `X-Frame-Options: DENY`then it can't be in iframe, 	   `X-Frame-Options: SAMEORIGIN` allows framing from pages of the same origin
	- CSP `Content-Security-Policy`:
		   `Content-Security-Policy: frame-ancestors 'none';` prevent any site from framing the page
		   `Content-Security-Policy: frame-ancestors 'self';` will allow the current site to frame the page:
	- `SameSite` cookies: When the `SameSite` flag on a cookie is set to `Strict` or `Lax`, that cookie won't be sent in requests made within a third-party iframe
> 	This means that any clickjacking attack that requires the victim to be authenticated, would not work, even if no HTTP response header restricts framing, because the victim won’t be authenticated in the clickjacked request.

### hut for it 
- [ ] collect pages contains state-changing actions. 
- [ ] check that the action can be achieved via clicks alone. or a URL
	> Attacks that require users to explicitly type in values are possible, but generally not feasible because they require so much social engineering. think about [[CSRF]]
- [ ] go throw collected functions and check for `X-Frame-Options`, `Content-Security-Policy: frame-ancestors`, `SameSite` flag in HTTP response headers, **==if found no vulns there==**, if missing then start POC
- [ ] Craft an HTML page that frames the target page
	```html
	<HTML>
	 <head><title>Clickjack test page</title></head>
	 <body>
		 <p>Web page is vulnerable to clickjacking if the iframe is populated with the target page!</p>
		 <iframe src="URL_OF_TARGET_PAGE" width="500" height="500"></iframe>
	 </body>
	</html>
	```
- [ ] write POC using above POC, CSS skill and test it your self
- [ ] use [SET](https://github.com/trustedsec/social-engineer-toolkit/) to clone a website
- [ ] place your `iframe` in place that frequently clicked such `Accept That This Site Uses Cookies! pop-up.` consider the larger impact of the vulnerability.
- [ ] host it on http://pipdream.com/ or use Burp's [Clickbandit](https://portswigger.net/burp/documentation/desktop/tools/clickbandit) tool
### Bypassing Protections
-  `frame-busting` uses JavaScript code to check if the page is in an iframe
	```js
	if (top.location == self.location){ 
	// Allow framing. 
	} else{ 
	// Disallow framing. 
	}
	```
to bypass that using `double iframe trick`: 
- search for a location on the victim site that allows you to embed custom iframes. such share links on profile which included via `iframe` or embed videos, audio, images
- This trick works by framing your malicious page within a page in the victim’s domain, This way, both `top.location` and `self.location` point to victim.com.
labs: https://portswigger.net/web-security/clickjacking
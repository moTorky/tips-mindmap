### what's it
Because of the SOP(*same origin policy*), a script from page A can access data from page B only if the pages are of the same origin. Two URLs are said to have the same origin if they share the same protocol, hostname, and port number. 
#### how SOP protect us 
Your site issues a GET request to `a.example.com/user_info` to retrieve the victim’s personal information. Since your victim is logged into the bank, their browser automatically includes their cookies in every request it sends to a.example.com, even if the request is generated by a script on your malicious site. Unfortunately, because of the SOP, the victim’s browser won’t allow your site to read data returned from `a.example.com`

#### Bypass SOP Restrictions
now if `a.example.com` wanna send data to `b.example.com`, SOP not allow this data to sent cuz they not have some origin however the both are is belong to `example.com`,so developer work around that. 
The simplest way for websites to work around the SOP is to change the origin of a page via JavaScript. Setting the origin of two pages to the same domain using `document.domain="example.com"` in the pages’ JavaScript will enable the pages to share resources.
However, this approach has its limitations. First, you can only set the `document.domain` of a page to a superdomain; for example, you can set the origin of a.example.com to example.com, but not to `example2.com`. so they use following to bypass SOP
1. use [[CORS]]
2. use [[postmassages]]
3. [[JSONP]]: Now that CORS is a reliable option for cross-origin communication, sites no longer use JSONP as often
4. XSS: If an attacker can get a malicious script executed on the victim page, the script can access the victim page’s resources and data

### Hunting for SOP Bypasses
Determine If SOP Relaxation Techniques Are Used
1. Find CORS Misconfiguration
	1. look for any signs of cross-origin communication in your proxy;  then look for `Access-Control-Allow-Origin`
	2. send request using repeater after change origin header value to `null`, `yoursite.com`, `yoursite.theresite.com` , analyze the response search for value of `Access-Control-Allow-Origin` header 
2. Find postMessage Bugs 	
	1. Inspect page, then navigating to **Event Listeners** to find a message event listener for `postmassages()`
	2. test for send action 
		1. Create an HTML page with an iframe that frames the targeted page accepting messages,  If the target cannot be framed, open it as a new window instead `window.open`
		2. Try to send messages to that page that trigger a state-changing behavior 
	3. receive sensitive data by listens for events coming from the target page,
		``` js
		var sender_window = window.open("https://TARGET_URL", target_domain) 
		function parse_data(event) { 
		// Run some code if we receive data from the target 
		}
		 window.addEventListener("message", parse_data);
		```
3. Find JSONP Issues
	1. if you see a URL being loaded in a `<script>` tag with a callback function. look for `callback`, `jsonp` url parameters 
### what && how
*JSON with Padding (JSONP)* allows the sender to send JSON data as JavaScript code.  A page of a different origin can read the JSON data by processing the JavaScript.
The SOP allows the HTML `<script>` tag to load scripts across origins
#### example
`a.example.com/user_info` have following 
```js
parseinfo({"username": "vickieli", "account_number": "12345"})
```
and `b.example.com` can get that data, by load JS code from `a.example.com` using 
```html
<script src="https://a.example.com/user_info?callback=parseinfo"></script>
<script> 
	function parseinfo()
	{
		//process the JSONP 
	}
</script>
```
You can usually find out if a site uses JSONP by looking for script tags that include URLs with the terms `jsonp` or `callback`.
#### risk
 - When JSONP is enabled on an endpoint, an attacker can simply embed the same script tag on their site and request the data wrapped in the JSONP payload
- if a user is browsing the attacker’s site while logged into a.example.com at the same time, the user’s browser will include their credentials in this request and allow attackers to extract confidential data belonging to the victim.
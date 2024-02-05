### what && how
 `postMessage()` is a JavaScript method. This method is a web API that uses JavaScript syntax. You can use it to send text-based messages to another window. 
where sender can use: `RECIPIENT_WINDOW.postMessage(MESSAGE_TO_SEND, TARGET_ORIGIN);`
The receiving window would then handle the message by using an event handler: `window.addEventListener("message",EVENT_HANDLER_FUNCTION);`

note that:
> Since using `postMessage()` requires the sender to obtain a reference to the receiver’s window, messages can be sent only between a window and its iframes or pop-ups.

> a window can use `window.open` to refer to a new window it opened. Alternatively, it can use `window.opener` to reference the window that spawned the current window. It can use `window.frames` to reference embedded iframes, and `window.parent` to reference the parent window of the current iframe

#### example 
window can use following to open new window and send data
```js
var recipient_window = window.open("https://b.example.com", b_domain); recipient_window.postMessage("{'username': 'vickieli', 'account_number': '12345'}", "*");
```
At the same time, b.example.com would set up an event listener to process the data it receives:
```js
function parse_data(event) { 
	// Parse the data 
}
window.addEventListener("message", parse_data)
```
#### misconfiguration
1. If the sender page doesn’t specify a target origin and uses a wildcard target origin instead, it becomes possible to leak information to other sites:
	```js
	RECIPIENT_WINDOW.postMessage(MESSAGE_TO_SEND, *);
	```
	attacker can create a malicious HTML page that listens for events coming from the sender page, and trick the user to trigger the `postmassege()` by load the page via image 
1. f the message receiver doesn’t validate the page where the `postMessage()` is coming from, it becomes possible for attackers to send arbitrary data to the website and trigger unwanted actions; attacker can send 
	```js
	function parse_data(event) { 
	// If "action" is "password_change", change the user's password 
	} 
	window.addEventListener("message", parse_data);
	```
	so attacker can embed or open the victim page to obtain its window reference. Then they’re free to send arbitrary messages to that window
	```js
	recipient_window.postMessage( "{'action': 'password_change', 'username': 'vickieli', 'new_password': 'password'}", "https://b.example.com");
	```
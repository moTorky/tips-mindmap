- include all the mistakes developers could make within the supporting security configurations of an API
- can happen at any level of the API stack, from the network level to the application level.
- like:
	- unpatched vulnerability lead to system takeover
	- default accounts for DBMS(`mysql -h <> -u <> -p`), or API login.
	- new API endpoints that are used only by the DevOps team and are not documented
	- Transport Layer Security (TLS) is missing. most common in a mobile application
	- lack of input sanitization, lead to injections or unrestricted file upload
	- A Cross-Origin Resource Sharing (CORS) policy is missing or improperly set.
	- Error messages include stack traces, or other sensitive information is exposed.
	-  poor transit encryption HTTP 
	- other problematic configurations. such Debug mode enabled
	- Security directives are not sent to clients (e.g., Security Headers). 
	  Misconfigured headers can result in sensitive information disclosure
	  Many API providers will use additional services alongside their API to enhance API-related metrics or to improve security
	  ex: `X-Powered-By`, `X-XSS-Protection: 0`, `X-Response-Time: <bignumber>` if the response time is big then the resource is exist even if we get 404
	- Unnecessary features are enabled (e.g., HTTP verbs trace, patch,..).
after u discover the API, collect some endpoint. u need to find out how to interact with that endpoint.
Before you craft requests to an API, you’ll need an understanding of its endpoints, request parameters, necessary headers, authentication requirements, and administrative functionality
#### find request info
1. API’s documentation:  
	1. search for publicly available Docs
		```links
		https://example.com/docs 
		https://example.com/api/docs 
		https://docs.example.com 
		https://dev.example.com/docs 
		https://developer.example.com/docs 
		https://api.example.com/docs 
		https://example.com/developers/documentation
		```
	2. create an account and use the token/cookie in fuzzing process, use [directory brute force](https://github.com/hAPI-hacker/Hacking-APIs) for fuzzing
	3. using your Google hacking skills
	4. use the Wayback Machine, will likely be outdated, but it should give you an idea of the authentication requirements, naming schemes, and endpoint locations
	5. try social engineering techniques to trick an organization into sharing its documentation, play the customer rule
2. specification: some API have it's docs in OpenAPI (Swagger), RAML, or API Blueprint or in a Postman collection, you can simply import it into Postman and review the requests that make up the collection 
3. reverse engineering: most of api not have public docs. so u need to open *capture a stream of requests* in postman, browse the app and click every btn to collect api requests as must as possible  
4. and we’ll use these sources to build Postman collections so we can perform analysis across each request.
### Analyzing Functionality
Once you have the API’s information loaded into Postman, you should begin to look for issues.
- you’ll seek out functionality that interests you as an attacker, especially if there are indications of information disclosure, excessive data exposure
- interact with resources, of the API that allow you to inject a payload, and administrative actions
- Can users use the API to generate a valid token without logging in?
- Do access tokens expire when updating or resetting passwords?
#### 1. Testing Intended Use
- start by use API as intended, use postman for that.
- use Documentation and Adjust your requests until you receive successful responses from the provider.
#### 2. Performing Privileged Actions
- try to find admin API documentation, test these actions in several phases; un-auth user, auth-user, and admin, 
- Privileged actions will often lead to additional functionality, information, and control.
- Making sure that the most basic security controls are in place 
#### Analyzing API Responses
- First check that you are receiving the responses you expect. API documentation can sometimes provide examples 
- then use the API in unintended ways, analyze and compare what u get to normal response
- At this point, your search for vulnerabilities begins, u can find information disclosures, security misconfigurations, excessive data exposures, and business logic flaw



tools 
- Swagger Editor: convert YMAL, JSON openapi to html page
- Mitmweb: cmd proxy
- postman Capture Requests Proxy: for constructing an API collection
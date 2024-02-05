#### what & how
> Like XSS and open redirects, insecure direct object references (IDORs) are a type of bug present in almost every web application. They happen when the application grants direct access to a resource based on the user’s request, without validation

> IDORs is access control vuln. happen when users can access resources that do not belong to them by directly referencing the object ID, object number, or filename.

> IDOR know as [[Broken Object Level Authorization(BOLA)]] in APIs, can be exist in REST, GraphQL
### prevention
>IDORs happen when an application fails at two things: 
>1. it fails to implement access control based on user identity.
>2. it fails to randomize object IDs

to prevent IDOR, first strict user access control, second use a unique, unpredictable key or a hashed identifier 
#### where to find
> ex: https://example.com/messages?user_id=1234. Now, what if you change the URL to https://example.com/messages?user_id=1233, look for functionalities that handle direct messages, personal information, and private content

> IDORs are not just limited to reading other users’ information, either. You can also use them to edit data on another user’s behalf, LIKE password reset, password change, and account recovery features
### hunt for it
1. Create Two Accounts for each permission level.
	>if the application has paid memberships, ask the company for a premium account or pay for one yourself. Quite often, paying for these memberships is worth it, because you gain access to new features to test.

	> use ==pwnfox== browser extension 
1. Discover Features 
	>try to discover as many application features as possible

	>Pay special attention to functionalities that return user information or modify user data, such read user messages, files,.. accessing group files, delete group
1. Capture Requests and Inspect each request carefully and find the parameters that contain numbers, usernames, or IDs.
	>you can trigger IDORs from different locations within a request, like URL parameters, form fields, filepaths, headers, and cookies.
1. Change the IDs in the sensitive requests and check if the information returned also changes
2. automate process using Burp intruder and go through IDs. [Auto Repeater](https://github.com/nccgroup/AutoRepeater/) and [AuthMatrix](https://github.com/SecurityInnovation/AuthMatrix/) allow you to automate the process of switching out cookies, headers, and parameters
3. use Burp extension [Autorize](https://github.com/Quitten/Autorize/) to test for Broken access control by accessing higher-privileged accounts with lower-privileged accounts

### Bypass IDOR Protection 
application start use complex id format such UUID which impossible to brute force it but u can find a list of leaked UUID, Here are a few places to pay attention to, beyond your plain old numeric IDs. 
- don’t ignore **Encoded IDs and Hashed IDs. for randomized ID**, see if the ID is predictable
  Entropy is the degree of randomness of the ID, The higher the entropy of a string, the harder it is to guess. Some IDs don’t have sufficient entropy and can be predicted after careful analysis
- Leaked IDs via another API endpoint or other public pages, like profile page leak all msg_ids 
- **Offer the Application an ID, Even If It Doesn’t Ask for One.**
>   In modern web applications, you’ll commonly encounter scenarios in which the application uses cookies instead of IDs to identify the resources a user can access. But some applications will implement an alternative way of retrieving resources, using object IDs. They sometimes do this for the convenience of the developers, for backward compatibility, or just because developers forgot to remove a test feature.
- Keep an Eye Out for Blind IDORs
> sometimes endpoints susceptible to IDOR don’t respond with the leaked information directly. They might lead the application to leak information elsewhere, like a generated PDF, or sent mail
- Change the Request Method
> If one HTTP request method doesn’t work, you can try plenty of others instead: GET, POST, PUT, DELETE, PATCH, and so on. Applications often enable multiple request methods on the same endpoint but fail to implement the same access control for each method.

> `GET example.com/uploads/user1236-01.jpeg` => `DELETE example.com/uploads/user1236-01.jpeg` , `PUT example.com/uploads/user1236-01.jpeg`, `POST example.com/uploads/user1236-01.jpeg`
- Change the Requested File Type
		`GET /get_receipt?receipt_id=2983` => `GET /get_receipt?receipt_id=2984.json`
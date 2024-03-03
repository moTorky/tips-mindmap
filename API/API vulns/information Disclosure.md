>APIs often leak sensitive data and the application logic of the hosting application. As you’ll see, this makes API bugs a widespread source of security breaches

information Disclosure: is an any information that attackers can leverage to their advantage, 
- review responses for software information(`X-powered-by`), usernames, email addresses, phone numbers, password requirements, account numbers, partner company names
- WordPress API `/wp-json/wp/v2/users` Disclosure users list   
- error msgs: software packages, operating system information, system logs, and software bugs. Generally, any information that can help us find more severe vulnerabilities or assist in exploitation can be considered an information disclosure vulnerability.
- API documentation can itself be an information disclosure risk. For instance, it is often an excellent source of information about business logic vulnerabilities
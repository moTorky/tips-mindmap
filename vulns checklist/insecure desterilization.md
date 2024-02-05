### what & why
- Insecure deserialization vulnerabilities happen when applications deserialize program objects without proper precaution. An attacker can then manipulate serialized objects to change the program’s behavior.
- it's hard to find and exploit, because it tend to look different depending on the programming language and libraries used to build the application.
- *sterilizing*: object mean convert the object to a byte stream
- *desterilize* mean convert a byte stream into object and load it into memory
- depending on context object injection allow attacker to perform different kind of attacks code injection , SQLi , path traversal, DOS
- Developers often trust user-supplied serialized data because it is difficult to read or unreadable to users. This trust assumption is what attackers can abuse.
The best way to understand insecure deserialization is to learn how different programming languages implement serialization and deserialization
#### PHP
When insecure deserialization vulnerabilities occur in PHP, we sometimes call them ***PHP** object injection vulnerabilities* 
> When an application needs to store a PHP object or transfer it over the network, it calls the PHP function `serialize()` to pack it up. When the application needs to use that data, it calls `unserialize()` to unpack and get the underlying object.
>**note that**: only the object's data is serialized not the function here's example:
```php
<?php
class User{ 
	public $username; 
	public $status; 
}
$user = new User;
$user->username = 'vickie'; 
$user->status = 'not admin';
echo serialize($user);
// O:4:"User":2:{s:8:"username";s:6:"vickie";s:6:"status";s:9:"not admin";}
?>
``` 
php serialize data format is `type:data`  here's list of data types:
```
b:THE_BOOLEAN; 
i:THE_INTEGER; 
d:THE_FLOAT; 
s:LENGTH_OF_STRING:"ACTUAL_STRING"; 
a:NUMBER_OF_ELEMENTS:{ELEMENTS} 
O:LENGTH_OF_NAME:"CLASS_NAME":NUMBER_OF_PROPERTIES:{PROPERTIES}
```
we can easily do privilege escalation by change the serialized object to 
`O:4:"User":2:{s:8:"username";s:6:"vickie";s:6:"status";s:5:"admin";}`
> If the serialized object isn’t encrypted or signed, then anyone can create a User object. even if signed the key can be leaked :D
##### `unserialize()` lead to [[RCE]] using *magic methods*
PHP *magic methods* is some methods that have a special functionality. If the serialized object’s class implements any method with a magic name, these methods will have magic properties, such as being automatically run during certain points of execution, or when certain conditions are met.

| method | discription | developer used it for |
| ---- | ---- | ---- |
| **`__wakeup()`** | auto called when an object is deserialized | reconstruct any resources that the object may have, reestablish any database connections that were lost during serialization, and perform other reinitialization tasks. |
| **`__destruct()`** | will be called when the object is no longer referenced or goes out of scope(object is no longer needed.) | deletes and cleans up files associated with the object |
| **`__toString()`** | when an object is converted to a string | return a custom string format for the object |
| **`__call()`** | executed when an undefined method is called on the unserialized object. ex: a call to `$object->undefined($args)` will turn into `$object->__call('undefined', $args)` |  |
more about [magic functions](https://www.php.net/manual/en/language.oop5.magic.php)
we can get [[RCE]] if the implementation of any magic functions call dangerous function such `eval()` on user-provided input. 
##### `eval()` example
```php
class Example2 { 
	private $hook; 
	function __construct(){ 
	// some PHP code... 
	} 
	function __wakeup(){ 
		if (isset($this->hook)) eval($this->hook); 
	} 
}
// some PHP code...
$user_data = unserialize($_COOKIE['data']);
```
POC 
```php
<?php
class Example2 {
	private $hook = "phpinfo();"; 
} 
print urlencode(serialize(new Example2));
```
##### POP Chains
but what if the declared magic methods of the class don’t contain any useful code in terms of exploitation? We have another way of achieving [[RCE]] even in this scenario: POP chains. A *property-oriented programming (POP)* , POP chains work by stringing bits of code together, called *gadgets*, POP chains use magic methods as their initial gadget. Attackers can then use these methods to call other gadgets. [phpggc](https://github.com/ambionics/phpggc) is a pre-built gadget chain tool ​
[for example application code](https://owasp.org/www-community/vulnerabilities/PHP_Object_Injection), other lab from [penterslab](https://www.notion.so/mortorky/API-to-Shell-11e3efbe3fe742d486d3c358beed5ecb?pvs=4)
#### JAVA
For Java objects to be serializable, their classes must implement the `java.io.Serializable` interface. These classes also implement special methods, `writeObject()` and `readObject()`, to handle the serialization and deserialization, respectively, of objects of that class. JAVA use `ObjectOutputStream`, `ObjectInputStream` classes contains several methods to write/read objects.
```java
import java.io.ObjectInputStream; 
import java.io.ObjectOutputStream; 
import java.io.FileInputStream; 
import java.io.FileOutputStream; 
import java.io.Serializable; 
import java.io.IOException;
class User implements Serializable{
	public String username; 
}
public class SerializeTest{ 
	public static void main(String args[]) throws Exception{ 
		User newUser = new User();
		newUser.username = "devmrt"; 
		
		FileOutputStream fos = new FileOutputStream("object.ser"); 
		ObjectOutputStream os = new ObjectOutputStream(fos); 
		os.writeObject(newUser); 
		os.close(); 
		
		FileInputStream is = new FileInputStream("object.ser"); 
		ObjectInputStream ois = new ObjectInputStream(is);
		User storedUser = (User)ois.readObject(); 
		System.out.println(storedUser.username); 
		ois.close();
	} 
}
```
> Java serialized objects are not human readable like PHP serialized strings.
> if the Java object is used as a cookie for access control, you can try changing the usernames, role names, and other identity markers that are present in the object, re-serialize it, and relay it back to the application
##### Achieving [[RCE]]
To gain code execution, you often need to use a series of gadgets to reach the desired method for code execution. This works similarly to exploiting deserialization bugs using POP chains in PHP, Finding and chaining gadgets to formulate an exploit can be time-consuming, also limited to the classes available to the application. To save time, try creating exploit chains by using gadgets in popular libraries, such as the Apache Commons-Collections, the Spring Framework, Apache Groovy, and Apache Commons FileUpload. You’ll find many of these published online. or simply use [Ysoserial](https://github.com/frohoff/ysoserial/) to generate payloads
`java -jar ysoserial.jar gadget_chain command_to_execute`
###### more java tools and repos
[JMET](https://github.com/matthiaskaiser/jmet) [Java serial killer](https://github.com/NetSPI/JavaSerialKiller)[ JavaDeserialization Scanner](https://github.com/federicodotta/Java-Deserialization-Scanner) , [Java deserialization Cheat-Sheet](https://github.com/GrrrDog/Java-Deserialization-Cheat-Sheet/) 
#### Ruby
- [Ruby 2.x Gadget chain](https://www.notion.so/mortorky/Ruby-2-x-Universal-RCE-Deserialization-Gadget-Chain-9c13e231f3ca42fd8cfdecf165154bff?pvs=4)
- [Ruby 2.x-3.x](https://devcraft.io/2021/01/07/universal-deserialisation-gadget-for-ruby-2-x-3-x.html)
#### .NET

#### python
### Prevention
1. not to deserialize any data tainted by user input without proper checks.
2. use simple data types, like strings and arrays, instead of objects that need to be serialized when being transported
3. check integrity of a serialized data
4. some developers mitigate deserialization vulnerabilities by identifying the commonly vulnerable classes and removing them from the application which restricts available gadgets attackers can use in gadget chains. ***this solution no effective cuz more gadgets can be found in other libraries* 
### hunt for it
1. white-box 
	- Conducting a source code review is the most reliable way to detect deserialization vulnerabilities. for PHP application, look for `unserialize()`, and in a Java application, look for `readObject()`. In Python and Ruby applications, look for the functions `pickle.loads()` and `Marshall.load()`
2. black-box/gray-box
	- Begin by paying close attention to the large blobs of data passed into an application.
		- base64 PHP serialized string start with `Tzo0O`
		- base64 representation of a serialized Python object of `class Person` with a name attribute of `vickie: gASVLgAAAAAAAACMCF9fbWFpbl9flIwGUGVyc29ulJOUKYGUfZSMBG5hbWWUjAZWaWNraWWUc2Iu`
		- Java serialized objects often start with the hex characters `AC ED 00 05` or the characters `rO0` in base64
		- Pay attention to the `Content-Type` header of an HTTP request or response as well. a `Content-Type: application/x-java-serialized-object` indicates that the application is passing information via Java serialized objects.
		- Look for features that might have to deserialize objects supplied by the user: database inputs, authentication tokens, and HTML form parameters.
- tamper the object try to get [[RCE]], Even when RCE isn’t possible, you might be able to achieve an [[Authentication Bypass]] or otherwise meddle with the logic flow of the application.


### more resources
- Deserialization vulnerability By Abdelazim Mohammed(@intx0x80) [[Deserialization Vulnerability.pdf]]
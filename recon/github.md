- gitGrapper `grap github tokens`
-  [Gitleaks](https://github.com/zricethezav/gitleaks/) to find secrets for u
- [Gitrob](https://github.com/michenriksen/gitrob/) locates sensitive files
- [TruffleHog](https://github.com/trufflesecurity/truffleHog/) specializes in finding secrets in repositories by regex searches
----------
1. get developers handle by searching the organization’s name or product names 
2. visit their pages. Find repositories related to the projects
3. Then dive into the code with attention to the **Issues** and **Commits** sections
4. check the _**Blame and History**_ sections
5. Search for 
	1. _key_, _secret_, and _password_ to locate hardcoded credentials, use [KeyHacks](https://github.com/streaak/keyhacks/) to validate it
	2. important functions such as authentication, password reset, state-changing actions, or private info reads. 
	3. code that deals with user input, such as HTTP request parameters, HTTP headers, HTTP request paths, database entries, file reads, and file uploads
	4. Look for any configuration files, as they allow you to gather more information about your infrastructure. 
	5. **old endpoints**, **S3 bucket**, **Outdated dependencies** and the unchecked use of dangerous functions are also a huge source of bugs
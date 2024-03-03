### what && how 
Information disclosure occurs when 
- an application fails to properly protect sensitive information, giving users access to information they shouldn’t have available to them. This sensitive information can include 
	- technical details , like software version numbers, internal IP addresses, sensitive filenames, and filepaths. 
	- It could also include source code that allows attackers to conduct a source code review on the application. on GitHub, GitLab or Pastebin 
	- the application leaks private information of users, like a user’s age, bank account numbers, email addresses, and mailing addresses, to unauthorized third parties.
	- informative code comments, hardcoding credentials 
### hunt for it
1. try [[Directory Traversal]]
2. Search the Wayback Machine for backup files `.bak`, configuration files `.conf`,`.env` files , any hardcoded credentials, and hidden endpoints. use `waymore` tool
3. Search Paste Dump Sites for leaking credentials like API keys and passwords. use Pastebin API, PasteHunter or [pastebin-scraper](https:// github.com/streaak/pastebin-scraper/) 
4. `.git` Directory 
	1. if this dir publicly accessible we can obtain an application’s source code and therefore gain access to developer comments, hardcoded API key , secrets ,.. 
	3. use [git-Dumper](https://github.com/arthaud/git-dumper) to downland accessible folder, then use `git` command to view the commits 
5. Find Information in Public Files(JS)
	1. links using [LinkFinder](https://github.com/GerbenJavado/LinkFinder/)
	2. grep for hardcoded credentials, API keys. download js files, use `grep password` , `grep api_key`
	
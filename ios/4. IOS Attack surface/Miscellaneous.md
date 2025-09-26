### Binary Cookies
- iOS applications using WebViews stores cookie data in binary cookies.
- Might contain login credentials and other sensitive information
- or the cookie never expire, or have a long time live.
- use `https://github.com/as0ler/BinaryCookieReader` 

### Logs
IOS application logs can contain sensitive information and data useful for a security assessment.
##### hunt on logs
- before start connect your IPhone to your mac to capture all logs use free `iMazing` app -> show device console
- after finish, search for:
	- tested username, password
	- domain name -> HTTP requests
	- account ID
- other tools: Cydia impactor, Xcode → devices an simulator → select device, show logs
- or use imazing → select device → tools → console

### screen snapshot
a screenshot takes by iPhone device when moving the app into multitask(background), if developer not implement splash screen or bluer, then it could leak sensitive infos
snapshots saved on data bundle: `private/var/mobile/containers/Data/Application/GUID/Library/Caches/Snapshots/`![[Pasted image 20250912161051.png]] 
### Hard secret
- Search through the codebase for hardcoded values that may indicate secrets. Look for keywords such as: _API_KEY_, _ACCESS_TOKEN_, _SECRET_, _PASSWORD_, _PASSWD_, _AUTH_, _KEY_, _TOKEN_, _PRIVATE_KEY_, _CLIENT_ID_, _CLIENT_SECRET_, _USERNAME_, _DB_PASSWORD._

### sensitive data in memory
Dump the memory of the application to check for sensitive information saved there
	> fridadump.py -U -r -s "APPLICATION_NAME"
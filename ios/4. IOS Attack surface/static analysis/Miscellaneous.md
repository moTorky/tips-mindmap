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
- other tools: Cydia impactor, Xcode

### screen snapshot
a screenshot takes by iPhone device when moving the app into multitask(background), if developer not implement splash screen or bluer, then it could leak sensitive infos
snapshots saved on data bundle: `private/var/mobile/containers/Data/Application/GUID/Library/Caches/Snapshots/`![[Pasted image 20250912161051.png]] 

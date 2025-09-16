### keychain
a sqlite DB but only accessible through the KeyChain APIs provided by Apple to store small parts of data e.g tokens, user sensitive data
- Keychain items are not wiped when an app is uninstalled.
###### Keychain Accessibility Attribute
first 3 attributes allow data backup
![[Pasted image 20250912142657.png]]
> sensitive data stored in key chain must be encrypted, cuz **jailbreaked** device can dump it.
test using 

tools: 
`https://github.com/ptoomey3/Keychain-Dumper`
``` objection
ios keychain dump
```
### iOS Data Storage
not encrypted bydefult
• NSUserDefaults: Can store small pieces of data, such as user preferences and application
settings. {lang, theame,..}
• CoreData: A framework that allows storing persistent app data.
• SQLite DB: iOS applications can use SQLite databases through an SQLite library.
• Realm DB: A local database, alternative to SQLite.
• Couchbase Lite: A lightweight database with a native iOS implementation.
• Plist: Files that store hierarchical serialized data, same to manifest.xml of android .
	a plist file could be embeded inside other plist file; use `plistsubstractor` for extraction
```
plutil -convert xml1 file.plist
```
- Interesting SQLite files
	![[Pasted image 20250912144749.png]]
	- search about google map api key in history `/private/var....com.apple.Maps....`

### Keyboard Cache
IOS caches user input in order to provide features such as form completion and personalized auto-correct.
- the application must not allow suggestion or autocorrection
	![[Pasted image 20250912150727.png]]
- Stored in `/private/var/mobile/Library/Keyboard/dynamic-text.dat` , use `strings`
### google maps api scanner


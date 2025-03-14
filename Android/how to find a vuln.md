1. start analysis from AndroidManifest.xml and map app components
	by using `grep -i <keyword> AndroidManifest.xml`: 
	- App permissions: `permission`
	- Backup allowance: `android:allowBackup` 
	- App components: `activity`, `service`, `provider`, `receiver`
	- Debuggable flag: `debuggable` 
1. after mapping go for interesting components, exported Activities (intent-filter),..
2. go to MainActivity.java
3. Use the app before u analysis it 
4. if u find interesting functionally start by
	1. find code for that function 
	2. explore all the different paths to that function 
	3. find entry point to the code u interested in 
	4. find out data what data its expect, send that data using POCApp or ADB 
5. patch app and make it Debuggable; makes understanding the program a lot easier.
	- `apktool d --no-src app.apk` and  add `android:debuggable="true"`
6. search in strings; start with keyword-based searching and go through the list only when keyword search does not help. keywords `password`, `key`, `secret` , use some words/labels from app itself


### hunt low hanging fruits
1. app Debuggable => https://developer.android.com/studio/debug/apk-debugger
2. app allow backup
3. exported activities


u can scan for above using `drozer` scanners



#### feature & test
***feature***: android 4 allow any app to read system log file for any app have "READ_LOG" permission
***test***: if the app support old versions such (android 4), make POC app have a "READ_LOG" permission and review it's logs search for information leakage(password, auth tokens)



https://xmind.app/m/GkgaYH/




#### non-understandable titles
- Testing Instant Apps
- Symbolic Execution

1. start analysis from AndroidManifest.xml and map app components
2. after mapping go for interesting components, exported Activities (intent-filter),..
3. go to MainActivity.java
4. Use the app before u analysis it 
5. if u find interesting functionally start by
	1. find code for that function 
	2. explore all the different paths to that function 
	3. find entry point to the code u interested in 
	4. find out data what data its expect, send that data using POCApp or ADB 

### hunt low hanging fruits
1. app debugable => https://developer.android.com/studio/debug/apk-debugger
2. app allow backup
3. exported activities


u can scan for above using `drozer` scanners



#### feature & test
***feature***: android 4 allow any app to read system log file for any app have "READ_LOG" permission
***test***: if the app support old versions such (android 4), make POC app have a "READ_LOG" permission and review it's logs search for information leakage(password, auth tokens)
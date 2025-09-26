> know if the application is swift or objective C
> the binary is under /var/containers/bundle/application
> this is done using XCODE tools or the jtool
	`nm BINARY_EXECUTABLE_FILE | grep '_OBJC_CLASS_$__'`
		>> if there is no result the application is written in Objective C
	`jtool2 -L BINARY_EXECUTABLE_FILE | grep libswift`
		>> if there is no result the application is written in Objective C

----
> Swift name Mangling 
	>> combining the classes, functions, methods, the binary, parameters, attributes ..etc in one line that ends with _T_
	>> `xcrun swift-demangle`
		>> this will demangle and return the normal writing of the swift code
	>>  Use `ipsw swift-dump <binary-name> --demangle > file.txt`
	>>  use `nm` , `llvm-objdump`

----
Applications are compiled normally without encryption, however when they are on app store they are encrypted according to Apple Fair Play Encryption to prevent the reverse engineering
To check the encryption
	`ARCH=arm64 jtool2 -l -v BINARY_NAME | grep -i crypt`
		>> here we check the flag of encryption, if it's 0 then it's not encrypted otherwise it's encrypted
	`ARCH=arm7 jtool2 -l -v BINARY_NAME | grep -i crypt`
		>> here we check the flag of encryption, if it's 0 then it's not encrypted otherwise it's encrypted

If the Binary is encrypted we need 

---
#### IPA decryption 
We can dump and decrypt the Binary in runtime using 
	>> 	`frida-ios-dump`
	>> need to change user,pass,host for script
	>> this is possible because the application must be decrypted before it can run
	>> but if the app didn't load due to security checks(jailbreak detection,..) then u need to get ipa file from the client

---
Thinning iOS binary 
	>> separate the multiple architecture Binary to muliple executables 
	>> done using archtype.c
	![[Pasted image 20250912191240.png]]

----
Retrieve class/method names 
`brew install blacktop/tap/ipsw`
for Objective C
	>> `ipsw class-dump BINARY_FILE_NAME --headers -o ./class_dump`
for Swift
	>> `dsdump --swift --objc --verbose=5 -a ARCHITECTURE BINARY_FILE_NAME`
	>> `ipsw swift-dump BINARY_FILE_NAME > ./swift_dump_mangled.txt`
	>> `ipsw swift-dump BINARY_FILE_NAME --demangle > ./swift_dump_demangled.txt`

-----
reverse engineering
- many disassemblers can used in this phase:
	- ida pro
	- hopper
	- gydra
- if u stuced on assembly use decompile to get bsudo-code for instructions: 
	- press `F5` or `fun+F5` for ida pro 
---
patching app

- Get the output from class dump 
- Search for the class/interface/function that's responsible for the detection
- Go to the functions and understand the checks made
- Try to change the return value or the return function 
	>> this has to keep the structure as it's, i.e if we will change a string make sure to replace a char with only one char
	>> the syntax must be the same as used by ARM assembly instruction, this is done by getting the hex value of the whole thing to be changed and translate it with ARMCONVERTER
	>> after getting the thing to change, the change is done in the HEX view
- Patch and Build the application again from Hopper using produce new executable
	>> this will produce new executable 
- Now we have to compress the whole PAYLOAD folder again to produce IPA again to be installed on the iPhone
	>> zip -r OUTPUT_NAME.zip Payload/
- After Compressing and converting to IPA
	>> transfer the app using iFunBox on the raw file system
	>> install the application using the AppSync

----
anti jailbreak detection bypass 
- Use `LibertyLite` Application from Cydia to bypass the Basic Implementations of JailBreak Detection
- frida script
- objection
- reverse enginner appears when u try all wutomated scripts but none works, so it's time to hit app reverse
----
Automating Analysis 
	>> Use MobSF somehow all in all solution
	>> Use Passion Fruit, works with frida 12 only

------
Dump and Decrypt the app in runtime if it's installed from App Store
Check the JailBreak Detection 
	> Tools in iPhone (cydia tweaks)
	> frida scipts(code share) and objection 
		> pass either the function to change its value or the address
	> get the classes and methods, search and patch on flex
	> static analysis(reverse it)
Understand the application Logic while loggin the application activity
Check anti-tampering  
Bypass SSL Pinning with SSL Kill Switch to intercept and test the webview, check if the requests and responses are logged
Check the Data Directory of the application
Dump the memory and search for sensitive data
Check the snapshot 
Check if the screenshot is allowed wwhile using the App
Check the cache and the suggestion and auto correction of sensitive text fields

----

side notes:
- most of ios tools don't work all time, so u need to search in the community, and keep updated
- all about try and error, in reverse, or in runtime manipulation
- don't assum somthing, untill u try it.
- for app crashing -> trace `exist` method, one of them used to exist the app , may in jailbreak, debuger.
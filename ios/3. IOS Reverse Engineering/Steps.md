1. extract ipa file
	1. use SSH connection
	2. https://medium.com/@M-Ali/get-ipa-of-any-app-which-is-available-on-app-store-395b15fc81ad
2. decompile
	1. use `ipsw` and `swift`
	2. dump the Objective-C classes from the binary:
	   `ipsw class-dump ./Payload/DVIA-v2.app/DVIA-v2 --headers -o ./class_dump`
	3. **ipsw swift-dump** command to extract Swift class information:
		```
		SWIFT_DEMANGLE="$(find /usr/libexec -type f -executable -name "swift-demangle")"

		ipsw swift-dump ./Payload/DVIA-v2.app/DVIA-v2 > ./swift_dump_mangled.txt
		ipsw swift-dump ./Payload/DVIA-v2.app/DVIA-v2 | $SWIFT_DEMANGLE --simplified > ./swift_dump_demangled.txt
		```
3. analyze
	1. ghidra, and ida pro :)
4. patch
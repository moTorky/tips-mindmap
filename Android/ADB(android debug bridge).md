![[Pasted image 20240304023058.png]]

- connect to deamon using TCP
    
    ```java
    adb connect <ip>
    ```
    
- list connected devices
    
    ```java
    adb devices -l
    ```
    
- get shell and run Linux commands `ls`, `ps` ,..
    
    ```java
    adb shell
    adb -s <device_name> shell
    ```
    
- run shell command inline
    
    ```java
    adb shell ps | grep -i "my running app"
    ```
    
- install apks
    
    ```java
    adb insatll myapp.apk
    ```
    
- uninstall apps using app’s package name
    
    ```java
    adb uninstall com.example.app
    ```
    
- upload files
    
    ```java
    adb push <locafil_epath> <emulator_path>
    adb push test /mnt/sdcard
    ```
    
- pull file/apk/dir
    
    ```java
    adb pull /data/app/<package_name>/base.apk .
    adb pull /mnt/sdcard/Downlaod/
    ```
    
- view system logcat
    
    ```java
    adb logcat
    add logcat --pid=$(adb shell pidof -s io.hextree.reversingexampl)
    adb logcat | grep "KEY"
    ```
    
- list mangers
    
    ```java
    adb shell service list
    Activity manager
    package manager
    ... manager
    ```
    
- package management
    
    - list all packages
        
        ```java
        adb shell pm list packages
        ```
        
    - get apk file path for running app
        
        ```java
        adb shell pm path <package name>
        adb shell pm path com.example.app
        adb shell pm list packages -f <app_name>
        ```
        
- activity manager `am` : can used to open any activity within app, which by pass any developer restrictions. 
    
    ```java
    adb shell am start -n <package>/.<activity>
    adb shell am start -n com.example.app/.loggedInActivity
    ```
- send click (touch screen) **input could send touch or fill input text**
	``` bash
	adb shell
	input tap <x> <y>
	# to find cordenites -> enable "pointer location" in devloper mode
	```
	![[Pasted image 20250706055540.png]]
- Bluetooth HCI (adb could help in extract Bluetooth stack logs)  **only work in real device**
	where Bluetooth stack logs is the way that devices used to transmit data by Bluetooth
	```
	1. connect to the bluetooth device (may it a tooth brush,...)
	2. use the app 
	3. dump the logs and open it using wireshark
	adb bugreport <file_name.zip>
	unzip <file_name.zip>
	wireshark <file_name>/FS/data/misc/bluetooth/logs/btsnoop_hci.log
	
	where Pixel 2 is our real device
	```
	![[Pasted image 20250706095633.png]]
- port forwarding 
	![[Pasted image 20250706094848.png]]
- reverse port forwarding
	![[Pasted image 20250706094825.png]]
- backup
	```
	adb backup -f all -all -apk -nosystem
	```
##### more resources: 
Github: https://gist.github.com/Pulimet/5013acf2cd5b28e55036c82c91bd56d8
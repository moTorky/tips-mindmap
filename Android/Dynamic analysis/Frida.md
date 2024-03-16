## [frida](https://frida.re/)

- open source tool support dynamic instrumentation for many platforms such android, iphone, linux, or windows
- used to inject JS snippets into application runtime 
- frida module can also load to a python script
### frida modes
1. **injected**: by run [frida-server](https://github.com/frida/frida/releases) on target and attach exiting process, or spawn new proc
	1. download a server that match your target from [frida-server](https://github.com/frida/frida/releases) , push and run it to your target `adb push frida_server /data/local/tmp` , `adb chmod +x /data/local/tmp/frida_server` , `adb shell ./data/local/tmp/frida_server &`
	2. install frida in your system `pip install frida-tools`
	3. check connection `frida-ls-devices`
2. **embedded**: repackage the target app with frida-gadget library
	1. used for know google/rooted/jailbrocken devices, here's how
		1. `apktool d app.apk` , then place frida-gadget in the lib dir
		2. add following to smali 
			```Smali
			const-string v0, "frida-gadget"
			invoke-static {v0}, Ljava/lang/System;->loadLidrary(Ljava/lang/String;)V
			```
		1. repackage and sing the app , or use Objection to automate all above
1. **preloaded**: by load frida library with the app 
### frida-tools
- `frida` used to attach process or spawn new one, get access to frida wrapper and execute JS commands
- `frida-compile` , `frida-create` for compile C modules
- `frida-discover` discover internal function for program
- `frida-ls-devices` list connected servers
- `frida-ps` list device process, that frida can attach, `frida-kill` kill process
- `frida-trace` to trace specific method/instruction or module such `libssl.so`
	- the mean of trace is once the app call a method from traced module, frida logged that call and provide handler file with `onEnter` , `onLeave` methods to hock that traced method  

#### `frida` command examples
- `frida-ps -U` list process for connected USB device
- `frida -U <process_name>` : attach process and get frida prompt(frida ripple), and run JS code
- `frida -U -l file.js <process_name>` attach JS script to process 
- `frida-trace -U -i "Java_*" <process_name>` : hock all method that start with `Java_`(java native calls) and create handler JS files under `__hanlers__` DIR  
- `frida-trace -U -I "libssl*" <process_name>` : hock all modules that start with `libssl`(which add SSL layer to connections), and create handler JS files under `__hanlers__` DIR for every method, every file have `onEnter` , `onLeave` methods.   ___used for SSL paining bypass___

#### frida ripple ([JS APIs and modules](https://frida.re/docs/javascript-api/))
- `Process` 
	- `id`: get attached process ID
	- `enumerateModulesSync()` : list all loaded libraries or modules associated with attached process
- `Java` API for java
	- `enumerateLoadedClassesSync()` list all loaded classes 
	- `enumerateLoadedClasses(callback)` list all loaded classes and use handler to interact with class using 
		- handler have to methods `onMatch: function(clsName){}` and 
		  `onComplete: function(clsName){}` 
	- `perform(fn)` : run function on attached process
	- `enumerateMethods(query)` list methods of a class. where query have form `"class!method"`
- `Socket`
- `Script`
- `Module`

#### python binding 
use python script to run JS with in it
```python
import frida
import time

device = frida.get_usb_device()
pid = device.spawn(["<package_name>"])
device.resume(pid)
time.sleep(1)

session = device.attach(pid)
script = session.create_script(open("file.js").read())
script.load()

input()
```
### JS API examples
##### hook method to change return value or log method args
```js
// frida -U -l file.js -f <package name>
Java.perform(function(){
	var activity = Java.use('<package>.<class>')
	activity.<target_method>.implementation= function (arg/*add or remove args based on target method implemntation*/){
		console.log('the app call target_method with following arg:'+arg)
		//if method return value, we can change returned value for it
		return true;
		//return 1337;
	}
})
```
in case of overload functions, use `.overload` keyword on method call:
`activity.overload().<target_method>.implementation= function (arg...`
##### access and change value of variables  
```js
// frida -U -l file.js -f <package name>
Java.perform(function(){
	Java.choose('<package>.<class>', {
		onMatch: function (instance){
			instance.<var_name>.value = new_value
		}
		onComplete: function(){
			console.log("value changed")
		}
	})
})
```
after we sow code for `Java.choose` and `Java.use` JS API functions what's diff between both?
> if u want to change the implementation of class' functions u can use `Java.use` cuz u want that change to effect all classes
> however if u want to access class' objects to modify it's behavior(call functions), or change the values of it's variables then u can use `Java.choose` to access instances of a chosen class  
##### invoice function 
```js
// frida -U -l file.js -f <package name>
Java.perform(function(){
	Java.choose('<package>.<class>', {
		onMatch: function (instance){
			instance.<method_name>();
			// use console.log to log retuned value
		}
		onComplete: function(){
			console.log("mehtod called")
		}
	})
})
```
> so what's diff, or which call is correct when called method on `Java.use`:
>  `files.getName();` => `Error: exists: cannot call instance method without an instance`
>  `files.getName.call();`  => `TypeError: cannot read property '$h' of undefined`
>  `files.getName.call(this);` => correct

##### HTTPS traffic analysis by attify
by hock read and write calls from openssl library, we can read data in plain text   
```js
//zomato-sniff.js
//frida -U -f com.application.zomato -l zomato-sniff.js --no-pause
//source: attify

var resolver = new ApiResolver('module');
var addr_SSL_read = resolver.enumerateMatches('exports:*!SSL_read')[0].address;
var addr_SSL_write = resolver.enumerateMatches('exports:*!SSL_write')[0].address;


Interceptor.attach(addr_SSL_read, {
    onEnter: function (args) {
        this.buf = args[1];
        this.num = args[2].toInt32();
    },

    onLeave: function (retval) {
        if (this.num>256) this.num=256;
        console.log('[*] SSL_read: app <- server')
        console.log(hexdump(this.buf, {length: this.num}));
    }
});


Interceptor.attach(addr_SSL_write, {
    onEnter: function (args) {
        const buf = args[1];
        const num = args[2].toInt32();

        console.log('[*] SSL_write: app -> server')
        if (num>256) num=256;
        console.log(hexdump(buf, {length: num}));
    }
});
```


more use cases and examples: 
- https://www.hackingarticles.in/android-penetration-testing-frida/

- `objection -g <package_name> explore`: get objection prompt
-  objection prompt have list of objects on `android` module such
	- `root` to pass root detection
	- `sslpinning` hook into certificate validation and make it return true
	- `hooking` for list classes, methods. change return values, log args
	- `memory` list loaded modules and it's exposes(functions)

python tool to analyze APK files, have many modules, https://androguard.readthedocs.io/en/latest, 
installations -> `pip install -U androguard`
usage -> 
1. `analyze`  provide 3 objects 
	1. `a` -> for apk -> all information about the APK, like package name, permissions, the AndroidManifest.xml or its resources.
	2. `b` -> [`DalvikVMFormat`](https://androguard.readthedocs.io/en/latest/api/androguard.core.bytecodes.html#androguard.core.bytecodes.dvm.DalvikVMFormat "androguard.core.bytecodes.dvm.DalvikVMFormat") corresponds to the DEX file found inside the APK file. You can get classes, methods or strings from the DEX file.
	3. `dx` -> analyze options -> all information about the classes, methods, fields and strings inside one or multiple DEX files.
	`a`, `dx` most used
2. `cg` -> call graph -> provide list of methods and graph for other methods used by that method, so it very helpful in obfestication code, cuz no matter what' the app's code look like, u still have graph how to reach certain method
	![[Pasted image 20250713084931.png]]
	`androguard.exe cg -o file.gml base.apk` -> that generate gml file can be opened with https://gephi.org/users/download/ `apt-get install openjdk-8-jdk`. `sudo update-altrnatives --config java`, `sudo update-altrnatives --config javac`
3. `decompile` -> flow graph -> regarding to single function, a function have tons of if conditions, switches,... so it's hard to follow along with, but with graph u can go backsword, forward to retch the goal![[Pasted image 20250713084906.png]]    `androguard decompile -o outputfolder -f png -i base.apk --limit "^Lcom.mobisec/"` **this may work only on kali/linux** png created with very big size so u gonna need to open it with `gimp`
	![[Pasted image 20250716155601.png]]then u can start analysis buttomUP or topDown  => [[demo upon mobisec]]
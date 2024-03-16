# Broadcaster Receiver

> A broadcast receiver responds to the system-wide broadcasts of announcements (e.g. a broadcast announcing that the screen has turned off, the battery is low, a network access is allowed(wifi, data) etc.) or from Apps (e.g. to let other apps know that some data has been downloaded to the device and is available for them to use). Broadcast receivers don't have UIs but they can show notification in the status bar to alert the user. Usually broadcast receivers are used as a gateway to other components of the app, consisting mostly of activities and services

Because the system runs each app in a separate process, one app cannot directly activate another app's components, however the Android system can. Thus to start another app's component, one app must send a message to the system that specifies an intent to start that component, then the system will start that component.

Broadcaster Receiver can dynamically defined using the `registerReceiver` method, where permission passed as arguments

receivers are primarily used to processing input from other apps, u need to check

- if app filter received data in implementation of `onReceive()`
- are the app limiting which app’s broadcast your receiver accepts [androidManifest.xml permission]
- `getResultX`

[file:///F:/Courses/04-Android/Udacity%20-%20Android%20Developer%20Nanodegree%20nd801%20v7.0.0/Part%2002-Module%2005-Lesson%2007_Background%20Tasks/26.%20Broadcast%20Receivers.html](file:///F:/Courses/04-Android/Udacity%20-%20Android%20Developer%20Nanodegree%20nd801%20v7.0.0/Part%2002-Module%2005-Lesson%2007_Background%20Tasks/26.%20Broadcast%20Receivers.html)

-----
MOBISEC LAB SOLVE OUTCOME:
after we know about the tow ways of declaring Broadcaster Receiver, here's difference between them:
- When you declare a receiver in the manifest, it's considered a static receiver.
- Starting from Android 8.0 (API level 26), implicit broadcasts are restricted due to background execution limits. Apps targeting API level 26 or higher can't register broadcast receivers for implicit broadcasts in their manifest unless the broadcast is sent specifically to them.
cuz of that we can only receive Broadcast listed [here](https://developer.android.com/develop/background-work/background-tasks/broadcasts/broadcast-exceptions) and we can't receive Broadcast with custom action. so we need to use **Programmatically registered receivers (`registerReceiver()` method):** in order to receive Broadcast with custom actions
LAB LINKS:
- https://challs.reyammer.io/challenges#justlisten-9
- SOL USING **manifest way**: https://challs.reyammer.io/apk/63971
- SOL USING **Programmatically registered receivers**: https://challs.reyammer.io/apk/63970
- chatGPT: https://chat.openai.com/c/a83095e6-6780-4f43-981e-dcb43c272b2a

# Services

> A service runs in the background to perform long-running operations or to perform work for a remote processes. A service does not provide any UI, it runs only in the background with the User's input. (e.g. a service can play music in the background while the user is in a different App, or it might download data from the internet without blocking user's interaction with the Android device.)

> we can start service by 3 ways:
> 
> - start it by `startService()` method, service runs until `stopService()` invoked
> - schedule a job
> - or bind a service
> 
> when a component bind a service, the service act as server and component is the client, using this way the client can communicates with the service back and forth.
> 
> an example: using service to display an audio, service can send the displayed audio, other infos, such as time, also the activity can send actions to the service such pause audio when user clicks a button⇒ [https://developer.android.com/guide/components/bound-services.html](https://developer.android.com/guide/components/bound-services.html)

service starts by send an intent to method such `startService()`, `bindService()`, as a security researcher that **intent** is first place to look for vulns. you will need to take look for any sensitive functionality invoked because of that **intent**

[file:///F:/Courses/04-Android/Udacity%20-%20Android%20Developer%20Nanodegree%20nd801%20v7.0.0/Part%2002-Module%2005-Lesson%2007_Background%20Tasks/index.html](file:///F:/Courses/04-Android/Udacity%20-%20Android%20Developer%20Nanodegree%20nd801%20v7.0.0/Part%2002-Module%2005-Lesson%2007_Background%20Tasks/index.html)
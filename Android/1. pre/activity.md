# Activities

> An activity represents a single screen with a User Interface(UI). An Android app may have more than one activity. All the activities in an App work together to create a User eXperience (UX).

u can define with Activity is the main in `AndroidManifest.xml`

### Activity Life Cycle

every activity have a life cycle, form the moment u open it to the end of closing the app.

but how android know which thing u want to run at which state of activity life cycle?

every activity have some methods that u can implement, then depended on the state of the activity android call the method.

here is list of methods and life cycle path. as a developer every method is important, however as a pentester u may write it all in `onCreate()`

```java
onCreate()       
onStart()       
onResume()
onPause()
onStop()
onRestart()
onDestory() 
```

**Intent** is the way to move from one activity to other, this intent can have data passed with it to the other activity, if this data is sensitive in nature, you would have an _information leakage vuln_. this is exploitable by all application capable of communicating with the activity

###### exported activities
so can `A.activatyA` starts `B.activatyB`:   yes if
1. `android:exported=true` defined as attribute on the `activity` tag in `manifest.xml`
2. an `intent-filter` defined inside `activity` that not define value for `android:exported`.
   ```xml
   <activity>
	   <intent-filter />
   </activity>
   ```
   so if `activity` have defind `android:exported=false` then it only allow intents from same app

[file:///F:/Courses/04-Android/Udacity%20-%20Android%20Basics%20Nanodegree%20by%20Google%20v1.0.0/Part%2003-Module%2001-Lesson%2001_Intents%20and%20Activities/index.html](file:///F:/Courses/04-Android/Udacity%20-%20Android%20Basics%20Nanodegree%20by%20Google%20v1.0.0/Part%2003-Module%2001-Lesson%2001_Intents%20and%20Activities/index.html)
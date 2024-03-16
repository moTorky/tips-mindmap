# **Content Providers**

### Why use Content Provider ?

1. make abstraction layer between UI code and data store, which help to hide the way app use to store data (files, SQLite, push to web, other persistent storage )
2. share data with other apps, allowing App1 to access/modify App2’s data

**resources:** Udacity-Android Nanodegree [Why use Content Providers.mp4](file:///F:/Courses/04-Android/Udacity%20-%20Android%20Basics%20Nanodegree%20by%20Google%20v1.0.0/Part%2005-Module%2001-Lesson%2004_Introduction%20to%20Content%20Providers/02.%20Why%20use%20Content%20Providers-5pqocYW-wws.mp4)

follow this module to implement your own Content Provider: [Link](file:///F:/Courses/04-Android/Udacity%20-%20Android%20Basics%20Nanodegree%20by%20Google%20v1.0.0/Part%2005-Module%2001-Lesson%2004_Introduction%20to%20Content%20Providers/index.html) or from [AndroidNotes](file:///F:/Courses/books/AndroidNotesForProfessionals.pdf) 696page

### How to use Content Provider to access other app data? `POC`

1. Get permission to use the Content Provider, by add permission to your app poc Manifest.xml
    
    ```xml
    <uses-permission android:name="com.example.udacity.droidtermsexample.TERMS_READ" />
    ```
    
2. Get the `ContentResolver`
    
3. Pick one of four basic actions: query, insert, update, delete “depending on your POC”
    
4. Use a URI to identify the data you are reading or manipulating
    
    ```java
    ContentResolver resolver = getContentResolver();
    Cursor mData = resolver.query(DroidTermsExampleContract.CONTENT_URI, null, null, null, null);
    /**
    * TVContract.Shows.CONTENT_URI returns valuse like 
    * content://com.example.udacity.droidtermsexample/terms
    * get URI structure from Contract class online Docs
    */
    ```
    
5. In the case of reading from the `ContentProvider`, display the information in the UI using Cursor methods
    

_**grantUriPermission**_

**resources**:

follow this module to read data from other apps using Content Provider: [Link](file:///F:/Courses/04-Android/Udacity%20-%20Android%20Developer%20Nanodegree%20nd801%20v7.0.0/Part%2002-Module%2005-Lesson%2004_Content%20Providers/index.html)
- show you all the directory information of the app.
	`objection -g sg.vp.owasp_mobile.omtg_android explore`
- patch app for Dynamic Analysis on Non-Rooted Devices ```
	objection patchapk --source app-release.apk
- by pass certificate pinning
	`android sslpinning disable`
occurs when an API consumer includes more parameters[`"isadmin": true`] in their requests than the application intended and the application adds these parameters to code variables or internal objects
##### to test for it all u need is find these parameters 
- find interesting parameters in API documentation and then adding those parameters to a request
- parameters involved in user account properties, critical functions, and administrative actions
- Intercept API requests and responses could also reveal parameters worthy of testing 
- fuzzing
like web Broken Access Control vuln
occur when an API provider allows an API consumer access to resources they are not authorized to access. ex UserA/un-authUser able to successfully request UserB’s resources.
##### techniques used to test for it
you can test for BOLAs by understanding how an API’s resources are structured and attempting to access resources you shouldn’t be able to access.
```php
if u found this                           then u can test
GET /api/resource/1                       GET /api/resource/3 
GET /user/account/find?user_id=15         GET /user/account/find?user_id=23 
POST /company/account/Apple/balance       POST /company/account/Google/balance 
POST /admin/pwreset/account/90            POST /admin/pwreset/account/111
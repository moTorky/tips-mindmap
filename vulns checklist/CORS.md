### what
allows servers to explicitly specify a list of origins that are allowed to access its resources via the HTTP response header `Access-Control-Allow-Origin`

##### example
let’s say we’re trying to share a JSON data from `a.example.com/user_info` to `b.example.com`: using CORS, the user’s browser will send an Origin header on behalf of `b.example.com`, browser send `Origin: https://b.example.com` header in the request to `a.example.com/user_info` `a.example.com` will send the browser the requested resource along with an `Access-Control-Allow-Origin: b.example.com` header. This header will indicate to the browser that a specific origin is allowed to access the resource

#### common misconfiguration
`a.example.com` allow all domains to access the resource if
1. `Access-Control-Allow-Origin: *`
2. `Access-Control-Allow-Origin: null` the browser will allow any site with a null origin header to access the resource.
3. set the `Access-Control-Allow-Origin` header to the origin of the requesting page without validating the requestor’s origin
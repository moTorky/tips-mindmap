- Without limiting the number of requests consumers can make, an API provider’s infrastructure could be overwhelmed by the requests.
- requests without enough resources will lead to the provider’s systems crashing, (DoS) state
#### test for it
1. you can do so by sending a barrage of requests to the API. If rate limiting is functioning, you should receive response informing you that you’re no longer able to make requests, or HTTP 429 status code. 
2. Once you are restricted from making additional requests, it’s time to attempt to see how rate limiting is enforced. Can you bypass it by adding or removing a parameter, using a different client, or altering your IP address?
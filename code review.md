you can follow tow approaches  if u want to discover vulns in code,
1. go after every user input, check if it get passed to unsafe function such `evil()`,..
2. find any unsafe function, map back throw your app and find if any user input get passed to it
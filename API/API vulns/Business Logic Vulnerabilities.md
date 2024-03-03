Vulnerabilities of this sort normally come about from an assumption that API consumers will follow directions, be trustworthy, or only use the API in a certain way
- You can search API documentation for telltale signs of business logic vulnerabilities. Statements like the following should illuminate the lightbulb above your head: 
  “Only use feature X to perform function Y.” 
  “Do not do X with endpoint Y.” 
  “Only admins should perform request X.” 
  "all file formats are accepted" => test [file-ext](https://github.com/hAPI-hacker/Hacking-APIs/tree/ main/Wordlists) , try to execute them
  make sure to disobey such requests to test for the presence of security controls.
- Another business logic vulnerability comes about when developers assume that consumers will exclusively use a browser to interact with the web app, or mobile app
> Study the application’s business logic with an adversarial mindset, and try breaking any assumptions that have been made.

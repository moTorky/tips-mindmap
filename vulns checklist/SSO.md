### what && how
*Single sign-on (SSO)* is a feature that allows users to access multiple services belonging to the same organization without logging in multiple times. Once you’ve logged into a website that uses SSO, you won’t have to enter your credentials again
Cookie sharing, [[SAML]], and [[OAuth]] are the three most common ways of implementing SSO
### Cookie sharing
Modern browsers allow sites to share their cookies across subdomains if the cookie’s Domain flag is set to a common parent domain
`Set-Cookie: cookie=abc123; Domain=facebook.com; Secure; HttpOnly` this cookie will sent to `*.facebook.com` 
### subdomain takeover
since browser's will send shared cookie to subdomains, if attacker take control of subdomain then he can get cookies all visitors 
###### example
say an organization wants to host its subdomain, `abc.example.com`, on the GitHub page `abc_example.github.io`. The organization can use a DNS CNAME record to point `abc.example.com` to `abc_example.github.io` so that users who try to access `abc.example.com` will be redirected to the GitHub-hosted page, now if the developers delete the `abc_example.github.io` content but didn't remove the *dangling CNAME* record from the DNS any one can register to `abc_example.github.io` and upload code to collect user's cookies
> but if someone find subdomain takeover, he not gonna use it for collect `shared Cookies` but phishing users 
#### hunt for it
The best way to reliably discover subdomain takeovers is to build a system that monitors a company’s subdomains for takeovers
1. List the Target’s Subdomains
2. Find Unregistered Pages, Even if you’ve found a dangling CNAME, not all third-party hosting providers are vulnerable to takeovers, such as Squarespace and Google Cloud, find list on [Can-I-Take-Over-xyz](https://github.com/EdOverflow/can-i-take -over-xyz/) 
3. Register the Page: Make sure to keep the site registered until the company mitigates the vulnerability by either removing the dangling DNS CNAME or by reclaiming the page on the third-party service.
4. monitor subdomain takeover
	- You never know when a site is going to be taken down and when a new dangling CNAME will be introduced into your target’s assets
	- after collect subdomains use [subzy](https://github.com/PentestPad/subzy) to check them 
	- use tmux, cron job to run every 1day
	- send notification to telegram, discord
```shell
go install -v github.com/LukaSikic/subzy@latest
./subzy r --targets subdomins.txt | grep "VULNERABLE"| notify
```
subdomain takeover verification: https://0xpatrik.com/takeover-proofs/
- [aquatone](https://github.com/michenriksen/aquatone)
- [SubOver](https://github.com/Ice3man543/SubOver)
- [subjack](https://github.com/haccer/subjack)

*Security Assertion Markup Language (SAML)* is an XML-based markup language used to facilitate SSO on larger-scale applications.
SAML enables SSO by facilitating information exchange among three parties: the user, the identity provider, and the service provider.
##### SAML flow
![[Pasted image 20240205163555.png]] 
SAML response contains an identity assertion that communicates your identity to the service provider. These are usually uniquely identifiable pieces of information, ex of use `username` in identity assertion
```XML
<saml:Signature> 
	<saml:SignatureValue> 
		dXNlcjE= 
	</saml:SignatureValue> 
</saml:Signature>
<saml:AttributeStatement> 
	<saml:Attribute Name="username"> 
		<saml:AttributeValue> 
			user1 
		</saml:AttributeValue> 
	</saml:Attribute> 
</saml:AttributeStatement>
```
SAML adds SignatureValue to prevent attackers from tamper the identity assertion, 
### vulns && attacks
- Sometimes the SAML signature isn’t implemented or verified at all!, 
- Other times, developers make the mistake of verifying signatures only if they exist
- signing mechanism used to generate the signature is weak or predictable like above sample where base64 used
- a SAML response is encrypted but not signed, or signed with a weak signature, attackers can attempt to tamper with the encrypted message
- tampering with encrypted messages without having to break the encryption, more about encryption attacks from [Wikipedia](https://en.wikipedia.org/wiki/Encryption#Attacks_and_countermeasures)
- SAML messages are also a common source of sensitive data leaks
- attackers can use SAML as a vector for smuggling malicious input onto the site. For example, if a field in a SAML message is passed into a database, attackers might be able to pollute that field to achieve SQL injection. Depending on how the SAML message is used server-side, attackers might also be able to perform XSS, XXE, and a whole host of other nasty web attacks.

### hunt for it
1. locate and search SAML Response for 
2. Analyze the Response Fields, tamper the data
3. try to Bypass  signature
##### TO-DO add XSW part



### H1 reports
SAML: hackerone.com/reports/2101076
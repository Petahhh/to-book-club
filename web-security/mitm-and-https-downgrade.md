# Man in the Middle Attacks And HTTPS Downgrade

## Man in the Middle Attacks

Http requests are vulnerable to man in the middle attempts. As an example, imagine joining a free wifi network. When visiting an HTTP website, your traffic can be intercepted without your knowing. Two main openings open up to harm you:
* your information is exposed to a party you did not consent to
* you believe you're viewing information from a website but the content may have been compromised (modified or completely wrong for malicious purposes)

Fun and scary fact:
* most devices remember networks you've trusted in the past and continue to trust them ... by network name
  * this means a free widely known network that you trust once, can be used against you by malicious actors posing with the same SSID
* tools like wifi pineapple can tell you what networks the people around you have joined
  * your devices announce what networks they trust
  * run wifi pineapple at a conference and you can determine the employment makeup of those around you
  * you don't even need to choose a widely used free wifi network, instead you can snoop what people already trust and compromise them with that information

When using free wifi, the first layer of domain name resolution is the router meaning you're putting a lot of trust into this public router. 

Once man in the middle attacks occur, things like CSRF tokens are compromised and XSS is much easier.

MITM attacks can occur with simple harddrive and not just on wifi networks but also cell networks too.

TLS protects against MITM attacks

## HTTPS Downgrade

An HTTPS Downgrade attack can happen when a user makes a HTTP request (which typically get redirected to HTTPS with a 301) but gets MITM attacked during this process. The server and the MITM are communicating securly but the user is still comunicating over HTTP directly with the attacker

A browser plugin can be used to securely upgrade requests when possible.

### Server Name Indication  (SNI)

Some certificates (like on cloudfare) have a large set of domains it is valid for. For this reason, SNIs, an exstension to TLS, can be used by the client to indicate which hostname they are attempting to connect to. The SNI is a mechanism for which a MITM can quickly forge a certificate with matching hostname.

### Strict-Transport-Security

The `Strict-Transport-Security` header in a response can be used to prevent http ever from being sent to the server.

Sites like HSTSpreload.org can be used to help users from attempting to reach your domain on http. Domains submitted to this site are collceted and are included in browsers to ensure HTTP requests are never made to them.

### Certicate Authority Compromise

Certificate Authorities can be compromised and it does happen. 
* private keys can get leaked and people start issuing fradulent certs
* wild card certs are signed and sold online


#### Public-Key-Pins

* Public key pins can be given in http response headers that contain metadata about the cert such that if the cert from the domain doesn't exactly match the pinned one, the browser knows not to trust it

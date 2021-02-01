# Cross-Site Request Forgery

* takes advantage of personal data saved on the browser used to make requests
  * personal data = cookies and basic auth credentials
  * using XSS you can make requests and the browser will use the cookies/basic auth
    * e.g. an element such as the following rendered unknowingly on a victim's browser may successfuly complete a get request `<img src="...transfers/peform?account=1&amount=2">`
  * post requests can also be completed
    * using social engineering one can fall victim to a hidden form by means of "Does this link work for you?" email/slack message
* CSRF attacks vulnerable when:
  * cookies are used for authentication or basic auth
    * except client side cookies: if information from a cookie is used in a request header it is not necessarily vulnerable from CSRF
    * if a website uses the cookie itself to identify the user then the site and it's users are vulnerable to CSFF

## CSRF Tokens

* a unique one time use token the server side provides in response such that a subsequent request will only be accepted if that exact token is provided
  * e.g.
    * user requests a password update form
    * server responds with a password update form and embeds the csrf token
    * user submits the form with the token already baked in
    * server receives the request and verifies the token is valid and completes the request
  * in the above workflow the token is only valid for a subsequent request and is not stored in a cookie making CSRF attacks harder

## Request Origin

* as best practice, validate request origins on the server side

## CORS (Cross-Origin Resource Sharing)

* enables a difference domain to make requests to the server on your behalf
  * Preflight Request: You send a set of headers to the server informing the server of an upcoming request with specs listed (origin, method, header types etc)
  * Preflight Response: The servers acknowledges
  * Main Request to Server: Another domain sends a request to the server and matches the specs from the preflight request

## Implementing CSRF Tokens

* with the express app given as an example, libraries such as `csurf` make it easy to insert middle where to handle token generation and validation
  * You just need to render the token in the form you're serving up and hook in the validation when processing the request
    * with csurf it's as easy as: `app.post('/process', parseForm, csrfProtection, function (req, res) { ...})`

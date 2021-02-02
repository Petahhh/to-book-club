# Click Jacking & Third Party Assets

## UI Redress attack

A form of click Jacking where a malicious actor gets their victims to click on their webpage but unknowingly they are interacting with another page. This is done by:
  * buy a domain and make a subdomain so that the hostname looks like a real site 
  * embed an iframe of the real site on the page and hide it
  * made a facade that entices the user to click a certain spot
  * if the user falls victim to this fake site, they end up completing an operation on another website without their knowledge
  * e.g. flash player did not have clickjacking protection and malicious actors managed to get users to disable their security settings

## Stopping clickjacking

`X-Frame-Options` is a HTTP response header you can specify if your response is permitted to be displayed in an iframe. In addition you can specify if it's allowed but only on the same origin or from a specific hostname. This header is respected by modern browsers but is not a guarantee.

Alternatively you can use JS to determine if your code is the top element in the browser. In the example the author recommends rerouting the user to your domain via `top.location = self.location`

## Third Party Assets

example:
* CDNs resources like bootstrap
* pulling in repositories like when you specify things in your package.json
* being asked to copy and paste script tags by a service provider to include in your site like google analytics

### Recommedations when using Third Party Assets

It's easy to take third party assets for granted. Bugs do creep up in popular libaries backed by big names. Recommendations for minimizing risk include:
* keep a lockfile so you don't let your build process automatically update new versions without your knowledge
* use Long Term Support versions when you don't need bleeding edge features
* in your CI/Acceptance tests, test the expected requests on page load
  * write a test that makes assertions on the number and type of requests you expect to occur. this way if unexpected requests slip into a newer version of the library you will learn about it immediately (and if you're using CI, immediately before going to production)
* when using CDNs, use SRIs  aka subresource integrity attributes)
  * in your `<script>`'s use the `integrity` attribute
    * this is a checksum ensuring the resource you're referring to is exactly what you expected (e.g. a malicious actor cannot update the resource at the same location maliciously without your knowledge)
  * in your `<script>`'s  use the `crossorigin="anonymous"` attribute
    * this prevents cookies from being sent over when the request is made
* when using vendor tags
  * vendor tags (block of html vendors ask you to include like google analytics)
  * add integrity attribute! (see above)
  * avoid scripts that add more scripts
    * the checksum strategy (by means of the integrity attribute) cannot be used if your script is importing more scripts
* tools like snyk helps scan your libraries of issues



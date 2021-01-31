# Web Security

Notes from the course Web Security by Mike North on frontendmasters.com

## Cross-Site Scripting (XSS)

Cross site scripting is ???

For example, the following EJS template will populate the html with a value held in JS. 

```
<h1> Welcome, <%- name %></h1>
```

If `name = "Mike"` nothing interesting will happen. However if `name = <script>someHarmfulFunction</script>;` the user who visits the site will have code run on their browser unexpectedly.

### Stored XSS

* Example: user input contains scripting that is persisted in a database
  * This is harmful because the code can get executed over and over again

### Reflected XSS

* Transient response from server causes script to execute
  * e.g. an error message pop up executes user code

### DOM Based XSS

* Doesn't require a server
  * e.g. pass code in query Param

### Blind XSS

* Exploits vulnerability in **another app**
  * e.g. intercepting a logger that is read by a non public facing app

### Locations for XSS 

* WYSIWYG where users enter text
* embedded content (e.g. iframe or flash embed)
* Anywhere users have access to a URL
  * e.g. old browsers users can inject js into the url
  * e.g. allowing users to provide css to change aesthetics is an opportunity for them to injeect code
* user's input is reflected back
  * e.g. error messages/warning messages that provide feedback on the user's input
* query params rendered into the DOM
* element.innerHTML 
  * when your javascript relies on reading content in the dom like this, you don't actually have control over what is actually in there even though your script can do dom manipulation. users can edit the dom and your script can pickup malicious code


### XSS Attack Demos

Using tools like BeEF and MITMF you can inject a code into the browser of a user going to a non https site. Once in place the mentioned tools can run arbitrary code on that user's browser. (Note: the user in this example was a machine on the same wifi network)

This was possible by what is known as ARP Spoofing. HTTPS will prevent this from happening.

By leaving your webapp insecure (http), you're letting your users become vulnerable to these hacks that can gain access to things they're logged in on in the internet.

### Things to look out for

* Open source libraries:
  * You want to see a private email to send security issues. Github issues aren't enough because those are immediately public. You will want an option to get help without publicizing the vulnerability just yet
  * Bonus: Do the library maintainers show you which XSS vulnerabilities they've already addressed?
* When looking at browser compatability, say for frameworks like Ember, having it _work_ (compile, tests succeed etc) isn't sufficient. Browser compatibility also encapsulates what vulnerabilities they've solved for. 
  * e.g. favJsFramework may support IE9 but you see your app running on IE8 okay. There may be an IE8 vulnerability that favJsFramework is susceptible too which can leave your users vulnerabile

* Browser plugins:
  * When you give plugins permission to modify content on any page, you're vulnerable to XSS from them (https).
  * Logging into your banking with plugins you haven't thoroughly scrutinized is a risk (recommend incognito always for banking)
  * "The easiest way to break into someone's machine" is through browser plugins
    * Browser plugins can be installed with no icon and collect data silently for a while

### Challenge 1 XSS Attack

* anywhere users can input text you could be vunerable to XSS
  * e.g. input boxes
```
git clone 

* the value in `<input>` was not sanitized for code. (specifically the accoun tname field) Users could input `<script>` tags. This meant that other users who do a read on this value have code injected into their browser neither the website or they were aware of.
* the query parameter in the profile page was read in:
  * you can inject `<script>` and the browser will execute it
  * modern browsers (e.g. newer versions of chrome) does a check comparing the URL with the code it has been asked to execute. If there is a verbatim match chrome will block it. For demo purposes the instructor appends a line of javascript with a string replace to bypass chrome's check.
  * alternatively you can bypass this by setting the `X-XSS-Protection` header to `0`

### User Data

**Never Trust raw user data**

* Do not put user data:
  * in html comment because user data can "end" the comment and inject their own behaviour.
  * Do not put user data in attribute names
  * Tag names
  * in `<style>` blocks

### Sanitizing User Data

* Sanitize data when:
  * data is persisted
  * and when data is rendered (js frameworks usually build it in)

* js frameworks have a way to render unescaped text
  * it's a good idea to check the syntax for your framework so you don't accidentally render unescaped text

* **keep user values as values, not as code**
  * do not store a user value and refer to it, directly use it as a value and do not let it become part of the code

### Content Security Policy (CSP)

* an http request header indicating the rules the browser should follow with respect to executing resources from particular domains
  * why:
    * browsers fetch resources and treat them all equally
    * by specifying which resources are safe to execute as code blocks the browser from executing code from sources you are not aware of
    * this control means a user's browser will only run code you explicitly spell out (with respect to metadata available in CSP such as type and domain source)
    * you could use this to block all resources froming from non https sources.
* `<script>` tags are the most common way people are vulnerable to XSS
  * sometimes it's convenient to use `<script>` so there are ways to mitigate this risk
    * define unpredictable nonce `<script nonce=....`> and put that in the CSP
      * this allows you to declaratively tell the browser which script tag is safe to run
    * provide a checksum for your script in the CSP

### Malicious Attachments

* Images like JPGs have data embedded in it. For example a jpg can have hidden html and if your code renders the image incorrectly you can spread malicious behavior when you think you're handling basic images.

Stopping malicious attachments:
* compressing images removes non visible data
* restrict file upload types
* understand file type capabilities
  * fun fact: pdfs can run javascript

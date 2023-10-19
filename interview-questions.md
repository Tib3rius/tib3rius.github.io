---
title: "Web AppSec Interview Questions"
---
<script src="https://code.jquery.com/jquery-3.7.0.min.js" integrity="sha256-2Pmvv0kuTBOenSvLm6bvfBSSHrUJ+3A7x6P5Ebd07/g=" crossorigin="anonymous"></script>
# Web AppSec Interview Questions

The following web application security questions and answers (mostly focused on web app hacking) are part of a series from my social media. Most are supposed to be difficult, so don't worry if you have trouble answering them! The answers are hidden by default so you can practice answering them yourself in preparation for an interview. Note that the answers aren't necessarily complete, they are just how I would answer the question.

## 1. What is the difference between Web Cache Deception and Web Cache Poisoning?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Web Cache Deception involves finding some dynamic page which you can access via a URL a web cache will automatically cache (e.g. if /transactions can be accessed at /transactions.jpg). If an attacker can trick a victim into visiting the cacheable URL, they can then load the same URL and retrieve the victim's information from the cache.<br /><br />Web Cache Poisoning involves finding an input which results in some exploitable change in the response, but doesn't form part of the cache key for the request. When an attacker sends their payload, the exploited response will be cached and then delivered to anyone who accesses the page.</div></div>

## 2. What two criteria must be met to exploit Session Fixation?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Session Fixation is not the same thing as Session Hijacking, but rather a type of Session Hijacking attack. The two criteria are:
<ol>
<li>Attacker must be able to forcibly set a (syntactically valid but otherwise inactive) session token in the victim's browser (e.g. using XSS / CRLF injection).</li>
<li>Once the victim authenticates, the application uses the session token already present and does not set a new one.</li>
</ol></div></div>

## 3. What are the differences between Base64 and Base64URL encoding?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">In Base64URL encoding, a "-" is used instead of a "+", and a "_" instead of a "/". Padding with = is also optional, and is usually omitted. This is to provide more compatibility if the value needs to be used in a URL.<br /><br />Did you know that padding is actually not required at all for decoding, even in regular Base64? This is because we can figure out how many bytes are left to decode based on the number of remaining Base64 characters:<br /><br />
2 characters = 1 more byte<br />
3 characters = 2 more bytes</div></div>

## 4. Name 5 (or more) types of Cross-Site Scripting.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">This was quite controversial and I was generally pretty lenient on what constituted a "type". The 5 I had in mind while writing the question were: Reflected, Stored, DOM-based, CSTI, and Server-Side.<br /><br />Other types suggested by people were: Self, XST, Universal, Blind, Mutation</div></div>

## 5. How does Boolean \*Error\* Inferential (Blind) SQL Injection work?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">This one confused a lot of people. Boolean Inferential (or Blind) and Error-based SQL Injection are two different things, but neither were what I asked about. I very specifically wanted the Error variant of Boolean Inferential injection.<br /><br />
This is a variant where injecting "AND 1=1" and "AND 1=2" (for example) will return the same response! The trick is to purposefully cause a database error when a condition we want to test is true, and hope that error propagates back to the response somehow (e.g. a 500 Internal Server error).<br /><br />
Many ways to do this, but most use a CASE expression and some divide by zero if the condition is true. For example: AND 1=(SELECT CASE WHEN (1=1) THEN 1/0 ELSE 1 END)</div></div>

## 6. What is the Same-Origin Policy (SOP) and how does it work?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">The Same-Origin Policy is a security mechanism browsers use to prevent a variety of cross-origin attacks. The basic principle is that client-side app code can only read data from a specific URL if the URL has the same origin as the current app. Two URLs have the same origin if they share the same protocol, host, and port.<br /><br />
Note that reading and embedding data from URLs are treated differently, allowing applications to embed things like scripts, videos, images, etc. without actually being able to access the raw bytes of each.</div></div>

## 7. How does the TE.TE variant of HTTP Request Smuggling work?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">The TE.TE variant has two or more servers which always use the Transfer-Encoding header over the Content-Length header if both are present, which usually makes Request Smuggling impossible. However, by manipulating the Transfer-Encoding header, it is possible to cause one of the servers to not recognize it. This server will use the Content-Length header instead, allowing the Request Smuggling attack to work.<br /><br />
There are countless ways to manipulate the Transfer-Encoding header. Common ones are including whitespace before the colon, capitalization, or modifying the value "chunked" in the header itself.</div></div>

## 8. What is DOM Clobbering and how can it be used to bypass (some) HTML sanitizers, resulting in XSS?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">DOM Clobbering is a way to manipulate the DOM using only HTML elements (i.e. no JavaScript). By using the id or name attribute of some elements, it is possible to create global variables in the DOM. This can lead to XSS in some cases.<br /><br />
I created a dynamic cheatsheet where you can see how DOM Clobbering works: <a href="https://tib3rius.com/dom/">DOM Clobbering Cheatsheet</a> (works best in Chrome)</div></div>

## 9. Describe how HTTP Parameter Pollution could be used to bypass a Web Application Firewall.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Some servers will concatenate parameter values if two or more identical parameters exist in requests, though often with a separator (e.g. a comma). For certain payloads, WAF detection can sometimes be bypassed if the payload can be split across multiple parameters.</div></div>

## 10. Describe IDOR and explain how mitigating it is different from other access control vulnerabilities.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Insecure Direct Object References occur when an application provides functionality to access a resource using some unique reference (e.g. an ID) but does not perform adequate access control checks to determine if the user should have access to the specific resource.<br /><br />
Generally, the user should be able to access the functionality, but not all resources via the functionality. Thus, mitigation involves an access check comparing the user to the specific resource being requested, as opposed to the functionality itself.</div></div>

## 11. What are JWKs and JKUs and how does their usage differ in JWTs?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">A JSON Web Key (JWK) is a JSON object representing a signing key in a JWT. A JSON Web Key Set URL (JKU) is a URL which points to the location of a set of JWKs. In a JWT, both JWKs and JKUs go in the header.<br /><br />
When using a JWK, the entire public key is embedded within the header, whereas a JKU can point to a set of multiple public keys. In both cases a key ID (kid) is used to select the key to be used.</div></div>

## 12. In the context of web apps, what is Business Logic and how does testing for Business Logic vulnerabilities differ compared to (for example) XSS, SQLi, etc?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Business logic is code which mimics real-world business operations / decisions, rather than code which handles how a user interacts with the application. Testing for business logic vulnerabilities usually involves identifying and challenging assumptions the developer has made about how someone uses the application, rather than technical oversights involving how data is processed.<br /><br />
It is impossible to identify business logic flaws using current scanners, since they require an understanding of the purpose of the application and are highly contextual.</div></div>

## 13. Describe 3 payloads you could use to identify a server-side template engine by causing an error message.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Invalid syntax: &#36;&#123;&#123;&lt;&#37;&#91;&#37;&#39;&quot;&#125;&#125;&#37;&#92;&#46;</li>
<li>Divide by zero: &#36;&#123;1&#47;0&#125;</li>
<li>Invalid variable names: &#36;&#123;tib3rius&#125;</li></ol></div></div>

## 14. What is the purpose of the Sec-WebSocket-Key header?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">The "key" has nothing to do with security / encryption. Since WebSockets are created using an initial HTTP request, the Sec-WebSocket-Key header is used by the client to make sure the server supports WebSockets. If the client doesn't receive a correctly hashed version of the key from the server, it doesn't continue with the WebSocket setup.</div></div>

## 15. What does the "unsafe-inline" value allow for if used in a script-src directive of a CSP?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">"unsafe-inline" will allow all inline scripts (e.g &lt;script&gt;...&lt;/script&gt; and "onevent" attributes) to be executed, but will not allow the loading of scripts from other files, nor will it allow the use of eval() and other methods which allow the execution of JavaScript from strings.</div></div>

## 16. Give an example of stateless authentication, and describe an inherent weakness of this authentication mechanism.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Authentication using a JWT is an example of stateless authentication. An inherent weakness of stateless authentication is the inability to forcibly expire user sessions, since all session information is stored on the client-side.</div></div>

## 17. Describe 3 ways to mitigate Cross-Site Request Forgery.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Setting the SameSite cookie attribute to Lax or Strict on session cookies can prevent this cookie being added to cross-site requests, making forged requests unauthenticated. There are some exceptions if Lax is used.</li>
<li>Requiring Anti-CSRF Tokens to be submitted with vulnerable requests will prevent CSRF provided the tokens are unique, unpredictable, and are not (only) submitted in cookies.</li>
<li>Another option is to check the Referer header of a request to ensure it matches a trusted origin.</li></ol></div></div>

## 18. What are XML parameter entities and what limitations do they have in XXE Injection?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">XML parameter entities are referenced using a % instead of &, but can only be referenced within a DTD, not the main XML document. This limitation means that parameter entities are often only useful with out-of-band XXE techniques.</div></div>

## 19. What recommendations would you give a customer for fixing DOM based XSS?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">If possible, avoid passing untrusted inputs to potentially dangerous JavaScript functions. Checks should be implemented to ensure that values only include expected characters (as opposed to trying to detect bad characters). Encoding inputs is also a possiblity.</div></div>

## 20. What conditions must be met to \*prevent\* a browser from sending a CORS Preflight request?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Only GET, HEAD, or POST methods are allowed.</li>
<li>Only the following headers can be manually set: Accept, Accept-Language, Content-Language, Content-Type, Range.</li>
<li>If Content-Type is set, it must use one of the following: application/x-www-form-urlencoded, multipart/form-data, text/plain.</li>
<li>If XMLHttpRequest was used, no event listener must be registered on the XMLHttpRequest.upload property.</li>
<li>No ReadableStream object was used.</li></ol></div></div>

## 21. Describe 3 ways an Insecure Deserialization vulnerability could be exploited.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Modifying the value of an object attribute.</li>
<li>Modifying the type of an object attribute.</li>
<li>Using a Magic Method to make calls to other functions/methods (potentially leading to RCE).</li></ol></div></div>

## 22. List the checks an application might perform to ensure files cannot contain malicious content, and can only be uploaded to specific directories.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Only allowing files with certain extensions and mime-types to be uploaded.</li>
<li>Performing file analysis (to confirm the file type) and AV scans.</li>
<li>Performing path canonicalization before checking the end location of the file matches an allowed directory.</li></ol></div></div>

## 23. How does Mass Assignment work and what are some potential outcomes of exploiting such a vulnerability?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Mass Assignment occurs when functionality allowing users to create or update "objects" does not restrict which attributes a user can specify. This is more common in modern MVC-type frameworks.<br /><br />This can lead to attackers being able to "upgrade" their role (e.g. to admin), add money to an account balance, assign potentially negative resources to other users, or perform a log forging attack by modifying date values, as well as countless other attacks.</div></div>

## 24. What is GraphQL batching and how can it be used to bypass rate limiting?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">GraphQL batching allows a user to send multiple queries or mutations to a GraphQL endpoint in a single request, either using arrays or aliases. Each query / mutation is then executed and a collection of results is returned in the response.<br /><br />This can bypass rate limiting since instead of sending 1000 requests to the endpoint (for example), one request can be sent containing 1000 queries / mutations.</div></div>

## 25. What is type juggling, and why does the JSON format help exploit these vulnerabilities?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Type juggling is a feature of certain programming languages where variables will be converted to a different type (e.g. string, integer, boolean) in certain operations, rather than throwing an exception. For example, when concatenating a string with an integer, the integer will be converted to a string.<br /><br />This can however lead to vulnerabilities when preserving the type is important. The JSON format helps exploit these vulnerabilities as it supports a wide range of data types natively (numbers, strings, booleans, arrays, objects, and nulls), whereas regular URL/Body parameters often only support strings and arrays.</div></div>

## 26. Describe 3 techniques you might use to find sensitive data being exposed by an application.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">There are of course far more than 3 techniques, and any of the following would count:<ol>
<li>Source code analysis.</li>
<li>Directory busting.</li>
<li>Causing errors / exceptions / stack traces by fuzzing.</li>
<li>Access control exploitation.</li>
<li>Google dorking.</li>
<li>Git repo history analysis.</li>
<li>Exploiting SQL injections.</li></ol></div></div>

## 27. Describe the attributes of a request which make it effectively immune to CSRF (i.e. CSRF mitigation is not required).

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Again there are a few possible answers here:<ol>
<li>If authentication uses an Authorization header and a non-trivial token (i.e. not Basic Auth), such as a JWT, or any kind of custom header with an unpredictable value.</li>
<li>If the server doesn't support CORS or has a locked down policy, and a non-standard HTTP method is used (e.g. PUT, DELETE), or the request body uses JSON/XML and requires an appropriate Content-Type.</li>
<li>If the request relies on a "secret" value which effectively becomes an anti-CSRF token. For example, login requests are immune to CSRF because if the attacker knows the victim's credentials, they don't even need to perform a CSRF attack*.</li></ol>
*There are some rare edge cases where performing a CSRF attack against a login, despite knowing the victim's credentials, would be useful.</div></div>

## 28. What are 3 negative outcomes (i.e. bad for the tester) that could arise if "OR &lt;true&gt;" (or similar) is relied on for SQL injection testing? 👀

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><a href="https://twitter.com/0xTib3rius/status/1616493354006495232">I've ranted about this before.</a><ol>
<li>OR &lt;true&gt; can return all rows of a table, which could cause server issues if the table is large.</li>
<li>OR &lt;true&gt; can lead to false positives when testing for login bypasses, if the login expects only one row be returned for a valid login attempt.</li>
<li>OR &lt;true&gt; injected into an UPDATE or DELETE statement can be disastrous.</li></ol></div></div>

## 29. Name 5 vulnerabilities which could potentially lead to OS command execution on a web app.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">There are quite a few ways, though several are rare or require highly specific setups to work:<ol>
<li>OS Command Injection</li>
<li>Insecure Deserialization</li>
<li>Server-Side Template Injection</li>
<li>File Upload Vulnerabilities</li>
<li>File Inclusion Vulnerabilities</li>
<li>Server-Side Prototype Pollution</li>
<li>Code Injection</li>
<li>SQL Injection</li>
<li>XXE</li></ol></div></div>

## 30. What is prototype pollution, and what exploits could it lead to with both client / server-side variants?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Prototype Pollution is a JavaScript / NodeJS vulnerability that allows attackers to add properties to global object prototypes, which are then passed down to actual objects used in the application.<br /><br />In client-side JS this can lead to DOM XSS. With server-side JS (e.g. NodeJS), it can lead to access control bypasses as well as potential RCEs.</div></div>

## 31. Describe how you would test for Vertical Access Control vulnerabilities on an application with 20 roles and 300+ different "functional" requests.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">While a manual effort is possible, the best way to do this is via some form of guided automation. In Burp Suite, the Auth Analyzer extension can be used to track multiple sessions (one for each role) and replay each request with updated session tokens, comparing the response to the original.<br /><br />For the brave, the AuthMatrix extension allows for more complex automation, and can handle logging users in, tracking anti-CSRF tokens, etc. Access rules can be configured per request/role pair, and the entire setup can be saved and replayed at a later date to validate fixes.</div></div>

## 32. Under what circumstances is a tab's Session Storage instance preserved?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">A tab's Session Storage instance is preserved if the page is reloaded, or if the user browses to another origin in the tab and later returns. If the user closes the tab, the instance is still preserved, provided the browser has the ability to reopen tabs.<br /><br />In some browsers, Session Storage for tabs is preserved if the browser instance crashes rather than exiting cleanly, allowing users to resume their browsing session.</div></div>

## 33. Other than uploading XML via a form, how else might one find and exploit XXE?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Many file formats use XML as a base and may trigger XXE if parsed insecurely. Examples include SVG, Microsoft documents (e.g. docx, xlsx), and other markup languages like KML.<br /><br />In addition, SOAP services use XML-formatted requests. In some cases, APIs which default to JSON-formatted inputs will also accept the same inputs as XML.</div></div>

## 34. Name some common password reset flow vulnerabilities.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Basing the password reset on a user identifier (e.g. username) rather than a secret token.</li>
<li>Using host header injection to modify password reset links in emails in order to steal the token.</li>
<li>Easily guessable password reset tokens (bonus if they don't expire quickly / once used).</li>
<li>Using security questions instead of a secret token to authenticate the user.</li>
<li>Username enumeration based on password reset success messages.</li></ol></div></div>

## 35. What is the difference between encoding, encryption, and hashing?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Encoding is the process of transferring data from one format to another while preserving the integrity of the data. If the encoding algorithm is known, anyone can decode the original data.<br /><br />Encryption is the process of scrambling data so that it can only be read by someone with the correct decryption key. Even if the encoding algorithm is known, unauthorized users will not be able to decrypt the data.<br /><br />Hashing is the process of converting data into a number (aka hash) of fixed size (e.g. 256 bits), such that the same data results in the same number. This can be used to verify a user knows the initial data without needing to know the data itself (e.g. a password for a login). The process is irreversible, and in good hashing algorithms, it should be difficult to find two sets of data which result in the same hash.</div></div>

## 36. Name some ways an attacker might exploit an HTTP Request Smuggling vulnerability.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Forcing a victim to trigger an XSS payload, including "unexploitable" payloads such as those contained within a UserAgent header.</li>
<li>Using some form of "save" functionality in the application to capture a victim's request, extracting their session token and hijacking their account.</li>
<li>Bypassing front-end access controls by smuggling a request to a disallowed area onto one of our own requests.</li></ol></div></div>

## 37. What is Server-Side Request Forgery and how can it be detected & exploited?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Server-Side Request Forgery (SSRF) occurs when an attacker can cause a server at the back-end of the application to make a "request" to a target it would not normally request from.<br /><br />It can be detected by looking for parameters which contain references to URLs, hostnames, or file paths, and attempting to manipulate these parameters to see if a request is made to a server we control, or to some backend service we can detect.<br /><br />SSRF can often be exploited to retrieve files from within the environment, perform basic port scanning, leak information from request headers, execute code, and even deliver XSS payloads.</div></div>

## 38. Name some ways TLS / SSL can be misconfigured.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Outdated Protocols (e.g. SSLv3, TLSv1.0)</li>
<li>Insecure Private Key Sizes</li>
<li>Incomplete Certificate Chains</li>
<li>Expired / Revoked Certificates</li>
<li>Insecure Cipher Suites</li>
<li>Lack of Forward Secrecy</li>
<li>Insecure Key Exchange Algorithms</li>
<li>Insecure Client-Initiated Renegotiation</li></ol></div></div>

## 39. Give some reasons why sending sensitive data in a URL query parameter is insecure.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>URLs are generally logged, by both the server and potentially proxy services in-between the user and application.</li>
<li>URLs are also saved to browser history, which may be preserved on shared public computers.</li>
<li>The data may be visible in screenshots and screen shares.</li>
<li>Users may think it is safe to copy URLs and share them.</li>
<li>If 3rd party resources are loaded by the client-side application, the data may get sent as part of the Referer header to the 3rd party.</li></ol></div></div>

## 40. In what ways could an open redirect be exploited?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>A victim could be redirected to a malicious copy of the site and not notice, since the original URL was for the legitimate site.</li>
<li>If chained with an SSRF, it could be used to bypass URL validation and reach otherwise prohibited targets.</li>
<li>If chained with a misconfigured OAuth setup, it could be used to steal access tokens.</li>
<li>If the redirect uses the Location response header, we may be able to perform CRLF injection.</li></ol></div></div>

## 41. Describe two output encoding techniques and the context in which they should be used to mitigate Cross-site Scripting.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Encoding for HTML contexts involves converting the following characters into HTML entities: &mp; &lt; &gt; " '</li>
<li>Encoding for HTML attribute contexts is the same, provided all attribute values are quoted correctly. If not, all non-alphanumeric characters should be converted to HTML entities.</li>
<li>Encoding for JavaScript contexts involves converting all non-alphanumeric characters into the Unicode encoding format (e.g. \u0022).</li></ol></div></div>

## 42. Describe three "403 Forbidden" bypass techniques.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Using different HTTP methods (e.g. POST instead of GET), or using "method override" headers / URL parameters (e.g. X-HTTP-Method) if a back-end server supports them.</li>
<li>Using "Client Origin" HTTP headers (e.g. X-Forwarded-For) to forge our source IP address, bypassing IP-based blocklists.</li>
<li>Manipulating the URL path using directory traversal, case modification, adding characters, or double-URL encoding.</li></ol></div></div>

## 43. Describe some potential CAPTCHA weaknesses.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Replay attacks - using a previously confirmed correct answer.</li>
<li>Improper input validation - removing or blanking CAPTCHA-related parameters.</li>
<li>Leaked answers - the correct answer appears somewhere in the source code (I once found a CAPTCHA which worked by using CSS to distort text).</li>
<li>Low entropy - if the set of possible answers is too small, a brute-force attack may work.</li>
<li>Machine learning susceptible - with enough training data, a computer can solve the CAPTCHA.</li></ol></div></div>

## 44. You find XSS in an application, however the customer informs you that users should be able to submit HTML code. What advice would you give them to remain secure?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">The easiest solution is likely to use an HTML sanitizer like DOMPurify with an allowlist of "safe" elements and attributes.<br /><br />Another option is to use a separate "sandbox" domain to host the HTML code, displaying it using an iframe. Any JavaScript code will run in the security context of the sandbox and will not be able to affect the main application.<br /><br />As an additional measure, a well-configured Content Security Policy can be used to instruct the browser to only run trusted JavaScript code.</div></div>

## 45. What are some questions you would ask a customer during a web app pentest scoping call?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Many questions would depend on a demo of the application, however here are a few general ones:<ol>
<li>How much functionality does the app contain (e.g. no. of "pages")?</li>
<li>How complex is the functionality (e.g. any learning curves, lengthy processes, etc.)?</li>
<li>How many different roles are there / should be tested?</li>
<li>Which environment is being tested (e.g. dev, staging, prod)?</li>
<li>Do our accounts have access to test/dummy data?</li>
<li>Are there any access restrictions (e.g. VPN, IP block)?</li>
<li>Are there any custom protocols being used (e.g. proprietary encoding/encryption)?</li>
<li>Is there any rate limiting, WAF/IPS in place?</li>
<li>Are there any out of scope areas, or vulnerabilities which should not be tested (e.g. Denial of Service)?</li></ol></div></div>

## 46. How would you recommend a customer fix an Insecure Deserialization vulnerability?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>If possible, don't pass serialized data via user inputs at all.</li>
<li>Use "safe" serialization methods (e.g. JSON, Protobuf).</li>
<li>Digitally sign any serialized data, and verify the signature prior to deserializing it.</li>
<li>If applicable, perform type checks against deserialized data prior to using it.</li></ol></div></div>

## 47. Name some user account enumeration techniques.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Error/success messages on login / registration / forgot password pages.</li>
<li>Insecure Direct Object References.</li>
<li>Timing Attacks (e.g. login).</li>
<li>Excessive data exposure on APIs (e.g. /v1/users).</li></ol></div></div>

## 48. Name some techniques to detect blind/inferential command injection vulnerabilities.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Trying commands with noticeable time delays, like sleep on *nix, or ping on *nix/Windows.</li>
<li>Attempting to redirect the command output into a file in the webroot (if we know / can guess the directory).</li>
<li>Trying commands which perform some detectable network interaction, like a DNS lookup (dig, host, nslookup) or HTTP request (curl, wget).</li></ol></div></div>

## 49. What are some types of race condition vulnerabilities in web applications?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Limit overrun - performing more actions than allowed (e.g. redeeming gift cards, transferring money).</li>
<li>State changes - bypassing a state change within normal application flow (e.g. a MFA step during login).</li>
<li>Resource access - accessing a shared resource prior to / during the processing of the resource (e.g. uploading and accessing a malicious file prior to AV detection).</li></ol></div></div>

## 50. How does NoSQL Injection differ from SQL Injection?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Other than the obvious (NoSQL injection affects NoSQL databases, not SQL databases), NoSQL injection is often highly dependent on the database variant and application programming language. Unlike SQL, there is no single standardized query language.<br /><br />NoSQL is also vulnerable to operator injection, which unlike regular syntax injection, can change the original nature of conditional checks in the query.<br /><br />Some NoSQL databases support the execution of arbitrary JavaScript code.</div></div>

## 51. Describe the syntax of an HTTP request.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">You can go into a lot of detail here, but here's a basic answer that hits all the key points:<br /><br />An HTTP request starts with a request line, which includes 3 parts separated by a single space: the request method / verb (e.g. GET), the request URI, and the HTTP version. The request line is terminated by a CRLF linebreak. After this, there are a series of headers which are optional apart from the Host header (in v1.1 and above). Each header is comprised of a name, colon, value, and finally a CRLF linebreak. After the final header, there is an empty line (i.e. a CRLF), and an optional body. If a body is included, its format and length is determined by information provided in the headers.</div></div>

## 52. Name some potential attacks against JWTs.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Lack of signature verification.</li>
<li>"none" algorithm support.</li>
<li>Accepting embedded / remote signing keys.</li>
<li>Brute-forcing weak keys.</li>
<li>Algorithm confusion.</li></ol></div></div>

## 53. Describe the process of finding and exploiting a web cache poisoning issue.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Identify unkeyed inputs (usually header / cookie values) using a tool like Param Miner.</li>
<li>Test identified inputs for client-side vulnerabilities (e.g. XSS, Open Redirect).</li>
<li>Send the payload to the server multiple times until it is cached by the web cache.</li>
<li>Verify the exploit by sending the request without the unkeyed input to see if the payload gets returned.</li></ol></div></div>

## 54. Describe the process of finding and exploiting a Server-Side Template Injection.

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden"><ol>
<li>Identify inputs which may end up in templates (either reflected or stored values).</li>
<li>Use a polyglot payload like $&#x7B;&#x7B;&lt;%&#x5B;%'"&#x7D;&#x7D;&#x25;&#x5C; to try and generate template errors.</li>
<li>Use several different arithmetic payloads (e.g. ${7*7}, {{7*7}}, &lt;%=7*7%&gt;) to try and detect / verify the version of the templating engine.</li>
<li>Check for known exploits of the templating engine for reading/writing files or performing OS command execution.</li></ol></div></div>

## 55. What is formula injection and how might it be exploited?

<div class="answer"><button class="toggle">Show/Hide Answer</button><div class="hidden">Formula injection, also known as "CSV Injection" occurs when an attacker can insert Excel-like formula (e.g. =1+1) into an application's CSV export functionality. Since most CSV files are opened in an Excel-like program, the formula will execute instead of displaying the raw data.<br /><br />This can be exploited by including a malicious formula which executes OS commands, for example the following which opens notepad.exe:<pre>=cmd|'/C notepad'!A1</pre>Other exploits can include data exfiltration via clickable links or DNS lookups.<br /><br />Formula injection is a relatively controversial vulnerability, since the actual exploitation takes place entirely on the victim's computer, and not within their browser (like XSS). In addition, multiple warning popups generally appear when a user opens a document containing executable payloads, and the user must "willingly" enable their functionality.</div></div>

<script>$(".toggle").click(function() {$(this).parent().children("div").toggle("fast", function(){});});</script>
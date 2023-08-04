---
title: "Web AppSec Interview Questions"
---
<script src="https://code.jquery.com/jquery-3.7.0.min.js" integrity="sha256-2Pmvv0kuTBOenSvLm6bvfBSSHrUJ+3A7x6P5Ebd07/g=" crossorigin="anonymous"></script>
# Web AppSec Interview Questions

The following web application security questions and answers (mostly focused on web app hacking) are part of a series from my social media. The answers are hidden by default so you can practice answering them yourself in preparation for an interview. Note that the answers aren't necessarily complete, they are just how I would answer the question.

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
<li>If the server doesn't support CORS or has a locked down policy, and a non-standard HTTP method is used (e.g. PUT, DELETE), or the request body uses JSON/XML.</li>
<li>If the request relies on a "secret" value which effectively becomes an anti-CSRF token. For example, login requests are immune to CSRF because if the attacker knows the victim's credentials, they don't even need to perform a CSRF attack*.</li></ol>
*There are some rare edge cases where performing a CSRF attack against a login, despite knowing the victim's credentials, would be useful.</div></div>

<script>$(".toggle").click(function() {$(this).parent().children("div").toggle("fast", function(){});});</script>
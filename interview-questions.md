---
title: "Web AppSec Interview Questions"
---
<script src="https://code.jquery.com/jquery-3.7.0.min.js" integrity="sha256-2Pmvv0kuTBOenSvLm6bvfBSSHrUJ+3A7x6P5Ebd07/g=" crossorigin="anonymous"></script>
# Web AppSec Interview Questions

The following web application security questions and answers (mostly focused on web app hacking) are part of a series from my social media. The answers are hidden by default so you can practice answering them yourself in preparation for an interview. Note that the answers aren't necessarily complete, they are just how I would answer the question.

## 1. What is the difference between Web Cache Deception and Web Cache Poisoning?

<div><button class="answer">Show/Hide Answer</button><div style="display:none">Web Cache Deception involves finding some dynamic page which you can access via a URL a web cache will automatically cache (e.g. if /transactions can be accessed at /transactions.jpg). If an attacker can trick a victim into visiting the cacheable URL, they can then load the same URL and retrieve the victim's information from the cache.<br /><br />Web Cache Poisoning involves finding an input which results in some exploitable change in the response, but doesn't form part of the cache key for the request. When an attacker sends their payload, the exploited response will be cached and then delivered to anyone who accesses the page.</div></div>

## 2. What two criteria must be met to exploit Session Fixation

<div><button class="answer">Show/Hide Answer</button><div style="display:none">Session Fixation is not the same thing as Session Hijacking, but rather a type of Session Hijacking attack. The two criteria are:
<ol>
<li>1. Attacker must be able to forcibly set a (syntactically valid but otherwise inactive) session token in the victim's browser (e.g. using XSS / CRLF injection).</li>
<li>2. Once the victim authenticates, the application uses the session token already present and does not set a new one.</li>
</ol></div></div>

<script>$(".answer").click(function() {$(this).parent().children("div").toggle("fast", function(){});});</script>
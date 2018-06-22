---
layout: post
author: Stefan Schwoegler
title: "AWS WAF Bypass"
description: "Bypassing AWS WAF using a single `;`"
keywords: "AWS WAF bypass"
---

# Introduction

I (finally!) got around to playing with [AWS WAF](https://aws.amazon.com/waf/) last November. My initial PoC was to grab [OWASP WebGoat](https://www.owasp.org/index.php/Category:OWASP_WebGoat_Project) and focus on the SQL injection module to see how it blocks those attacks. After spending a couple days of free-time without any blocks it slowly dawned on me that I was actually bypassing the attack. So thinking I had lost all my offensive skills, forgotten everything I had learned from [@infoseckeatron](https://twitter.com/infoseckeatron)'s (seriously outstanding!) [InfoSec Institue](https://www.infosecinstitute.com/) CEH bootcamp, I turned to some trusted web app developer friends of mine to check my work. When they came back with, "Nah, you're good, and you should submit that to Amazon for review!" I did so, and what follows is our journey to properly block these simple SQL injection bypasses.

# Disclaimer

This research was done entirely independent of any employer past or present.

# Vulnerability Disclosure Report

Below is my vulnerability disclosure report as sent to aws-security@amazon.com unedited; except for anti-spam email obfuscation. While the attachment is missing, a representative github repo of this journey is available at [enkaskal/aws-waf-sqli-bypass-PoC](https://github.com/enkaskal/aws-waf-sqli-bypass-PoC)

N.B. The UserData in this initial commit is likely out of date/wrong. At least I had to update it for the *Simplified Example* section (below); however at the time I swear it JustWork'd(TM)

~~~
-----Original Message-----
From: stefan _at_ cryp7 _dot_ net
Sent: Tue, 13 Feb 2018 05:28
To: aws-security@amazon.com
Subject: WAF SQLi bypass

Hello,
 
I was recently investigating the AWS WAF service and believe I have stumbled upon an SQLi bypass. Originally, I was just trying to ensure the rules were working correctly and 
I thought I was misconfiguring my test environment; however, after some troubleshooting it became clear that was not the case. While it may be considered a low priority issue 
that doesn’t warrant a vulnerability disclosure, I thought it best to err on the side of caution; especially since I don’t have an active support contract to submit via the 
ticketing system and didn’t want to post it publicly via the forums.
 
Attached is a tarball that includes the following:
 
* README.md
 * A report describing my findings.
* bypass/
 * Puppeteer scripts that attempt to automate the bypass (both numeric and string).
* cf/
 * A standalone CloudFormation template to assist in reproducing my findings.
* .sh
 * Helper (bash) scripts to assist the reviewer.
* demo.gif
 * An animated gif demonstrating the successful WAF bypass using the ssqli.sh helper.
 
Just in case the attachment is filtered for some reason, the same tarball is available via Dropbox at: [REDACTED]
 
SHA256SUM: b5bb12d38bb1ca930fd138730eae795229302cc93741e4e991af703b79c4524a
 
Finally, the automated scripts are _fairly_ deterministic, but I have found repeated invocations can demonstrate non-determinism that I believe is due to timeouts or (possibly) 
chrome headless crashing. The main point of the Puppeteer scripts was to codify the bypass and while I normally would’ve done some more testing and cleanup; QA was already 
dragging on and therefore I decided it best to just submit as is. However please don’t hesitate to contact me if you have any questions or would like me to pair/screen share with a reviewer.
 
Very Respectfully,
 
-- 
 
Stefan Schwoegler
~~~

# Initial Response

After ~1mo I received the following response:

~~~
-----Original Message-----
From: aws-security@amazon.com
Sent: Mon, 12 Mar 2018 20:56:54 +0000
To: stefan _at_ cryp7 _dot_ net
Subject: RE: WAF SQLi bypass

Hi Stefan,

Thank you for reaching out to us regarding the Security Issue. After reaching out to the service team, they concluded that the issue came from the fact that several sequential transforms
were used in your WAF configuration. Currently the API does not have a way to specify multiple sequential transforms. However, the documentation did not reflect this at the time of your report.
We have updated it and it is now available at the following link: https://docs.aws.amazon.com/waf/latest/developerguide/getting-started.html#getting-started-wizard-create-string-condition.
If you discover otherwise or become aware of another security concern specific to AWS products and services, please do not hesitate to contact us again at aws-security@amazon.com.

Thank you again for helping us protect our customers!

Best Regards

[REDACTED]
AWS Security
https://aws.amazon.com/security
~~~

N.B. Name has been [REDACTED] to protect the innocent. Also, there was typical "status" correspondences during the ~1mo so it's not like there was total silence during the interim.

# My Reply

~~~
-----Original Message-----
From: stefan _at_ cryp7 _dot_ net
Sent: Wed, 11 Apr 2018 02:44
To: aws-security@amazon.com
Subject: RE: WAF SQLi bypass

[REDACTED],

Thank you for your reply.

It was a pleasure working with you all, and given this response, plus the fact you have publicly posted your remediation I would like to publish this work to my personal blog.
I would like to include content from our correspondences but will not include any identifying information other than an AWS Security Support Representative. One thing that would 
be very helpful is if AWS could also provide the steps or a modified version of my submission that would block the SQL injection. This would really help me hammer home a good 
teaching example for the blog post.

Can you help in getting the improved solution or steps to that solution?

Can you also confirm that there is not an issue publishing my work to a personal blog with the caveats above?

Very Respectfully,

--
Stefan Schwoegler
~~~

# Their Response
~~~
-----Original Message-----
From: aws-security@amazon.com
Sent: Wed, 11 Apr 2018 16:10:06 +0000
To: stefan _at_ cryp7 _dot_ net
Subject: RE: WAF SQLi bypass

Hi Stefan,

Thank you for notifying us about the blog post. I will look into it and get back to you with a status update within 5 business days. 
In the meantime, if you have already drafted the blog post, we would greatly appreciate it if you could share this draft with us.

If you have any further questions or concerns, please do not hesitate to contact us. If you wish to protect your email, you may use PGP; our key is here:

https://aws.amazon.com/security/aws-pgp-public-key

If you would like AWS Security to encrypt its communications to you, please provide us with your public PGP key also.

Thanks again!

Best Regards

[REDACTED]
AWS Security
https://aws.amazon.com/security
~~~

# A Simplified Example

Given Amazon's response at this point I decided to put together a simplified example: [https://github.com/enkaskal/aws-waf-sqli-bypass-PoC/tree/simplified](https://github.com/enkaskal/aws-waf-sqli-bypass-PoC/tree/simplified)

It is a single rule WebACL with _ONLY_ a Body FieldToMatch + CMD_LINE TextTransformation which I feels highlights my findings.

I then sent it to them along with a draft of this blog post for review.

# Resolution

After providing the simplified example, and a draft of this blog post for review prior to publication, I was contacted by Principal/Senior AWS staff asking for a teleconference. During the call, they agreed with my initial findings/expectations, and asked that I halt publication so that we could continue to work together to properly mitigate these attacks. After some back and forth collaboration with the team, including ensuring that not only do compound SQL statements using a single ';' but also canonical solutions were also handled, I can happily report these injection flaws are now properly protected against in AWS WAF!

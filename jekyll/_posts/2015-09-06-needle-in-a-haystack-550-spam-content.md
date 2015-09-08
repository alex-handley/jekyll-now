---
layout: post
title: Missing Emails == Needle in a Haystack
date: 2015-09-06T09:38:00+00:00
tags:
- draft
---

I recently joined the Happiness squad at LostMyName and after a few days I was alerted by our customer service staff that some of our customers were not receiving emails.

## The Problem

At first I thought it was just a case of emails going into junk because Mandrill was indicating that the email had been delivered but it quickly became apparent that email were not making it to customers.

![Mandrill indicating delivered](/images/post_images/mandrill-delivered.png)
<br />
<caption>Mandrill indicating that the email was delivered successfully</caption>

My next step was to contact Mandrill to see if they had any suggestions, their response was that after they get a successful response from the email service provider there job is done.
Mandrill support suggested contacting the service provider, I wanted to try and avoid this as I knew from experience this would be a nightmare but at this point I had no other ideas so I emailed the spam email account.

Almost immediately I got a reply from the mail server saying the mailbox was full and could not store the email. Told you so.

At this point I was completely out of ideas. A colleague suggested that it could be the IP addresses used by Mandrill, I had considered this but had rejected because there were only a few hundred missing emails.

## The Cause

I emailed Mandrill enquiring about the IP addresses and at this point they said that the IP's were fine, the issue was the status code being sent back by the email provider:

![550 Spam Detected](/images/post_images/550-spam-detected.png)
<caption>Cheers Mandrill...</caption>

Mandrill admitted that the response wasn't getting parsed correctly and because of this emails were incorrectly being shown as delivered.
Now I knew the issue the actual debugging started, I looked at the email template and decided it was ether the images or the buttons (links inside tables).

To test this I needed access to an email account on the domain which was hard because it was a French ISP who only gave out accounts to customers. I lucked out and one of the customer services staff dad had an email account and the patience to handle me hounding him.

After trialling removing different elements I found it was the button. The next experiment was to see if sending test emails with a unstyled link and it worked!

![Spam Button](/images/post_images/spammy-email.png)
<caption>The problem</caption>

## The Solution

The problem made perfect sense in hindsight if you read the Mailchimp guide (in references), this button breaks two rules:

- Avoid bright red fonts or going crazy with colors.
- DON’T YELL IN ALL CAPS.

After changing the colour and removing the CAPS it worked perfectly!

![Fixed button](/images/post_images/fixed-button.png)
<caption>The Fixed Button</caption>

Hope this helps someone.

## References

- http://templates.mailchimp.com/concepts/spam-filters/

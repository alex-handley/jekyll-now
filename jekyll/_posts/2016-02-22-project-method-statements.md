---
layout: post
title: Project Method Statements
date: 2016-02-22T09:38:00+00:00
tags:
- Programming
---

I have been at my current job for just over 6 months and in that time our team has doubled in size, with that comes all of the usual problems that occur when a company started 2 years ago and now has over 100 staff and 20 engineers.
The growth pain I am going to talk about is how we get code from our computers to the live site, in the past i've worked on projects where deploying was as easy as merging the branch and typing ```git push heroku master``` or ```cap deploy``` if you're not funded :) and as complex as connecting to a VPN, pushing to a private Git server, emailing DevOps and waiting a few hours/days for your change to be deployed.

Both of these approaches are still vunerable to downtime caused by forgetting to migrate the database, they don't solve what happens when it fails? how do you rollback?
While I was thinking about this with our CTO a few weeks back it reminded me of a past project I worked on with an Asbestos removal company, the way they remove this extremely hazzardious material is so tightly regulated that they are required to send a document to UK government health and safety department about how they plan to do it.
This document is called a method statement, the same idea works for how we deploy our applications, there should be an agreed method for the safest way to deploy the application and how rollback when it goes wrong.

By writing down our method statement and agreeing it with the whole team, it means the whole team is accountable when something goes wrong, this doesnt necessarily mean the person who made the mistake gets sacked, it is more about finding out why? was the person deploying in a rush at 6PM cause a feature was snuck in mid sprint.
Now the we have a process that the whole team buys into and is accountable for, the next step is making it easy to update and make the process visible to people reviewing the pull request and it just so happens you can now do this with Github with <a href="https://github.com/blog/2111-issue-and-pull-request-templates">Pull Request Templates</a>.
What has been great so far is because the whole team including people not comfortable with the terminal can amend the process and submit a Pull Request using the GitHub Website.

![example pull request template](/images/post_images/pull_request_template.png)


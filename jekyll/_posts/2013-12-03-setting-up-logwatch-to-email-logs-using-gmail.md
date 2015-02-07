---
layout: post
title: Setting up Logwatch to email logs using Gmail
date: '2013-12-03T08:49:00+00:00'
tags:
- sysadmin
- logwatch
- Programming
---

I thought I would share this quick guide to setting up sending yesterdays logs without needing to install Sendmail or another heavy weight email client.

Step 1. Install our lightweight SMTP client.

    sudo apt-get install ssmtp

Step 2. Configure our client to talk to Gmail.

{% highlight bash %}
  sudo vim /etc/ssmtp/ssmtp.conf
{% endhighlight %}

The config for Gmail should look like this:

{% highlight bash %}
root=YOUR_EMAIL_ADDRESS
mailhub=smtp.gmail.com:587
rewriteDomain=YOUR_EMAIL_DOMAIN
AuthUser=YOUR_USERNAME
AuthPass=YOUR_PASSWORD
FromLineOverride=YES
UseTLS=YES
UseSTARTTLS=YES
hostname=smtp.gmail.com:587
{% endhighlight %}

Step 3. Check your configuration works

{% highlight bash %}
  cat - | /usr/sbin/ssmtp -v
{% endhighlight %}

Now press ctrl-d and you should see something like this:

{% highlight bash %}
354 Go ahead l4sm46964373een.13 - gsmtp
Received: by smtp.gmail.com:587 (sSMTP sendmail emulation); Tue, 03 Dec 2013 14:40:08 +0100
 From: YOUR_SENDER_EMAIL
 Date: Tue, 03 Dec 2013 14:40:08 +0100
{% endhighlight %}

You also should receive an email.

Step 4. Install Logwatch.

{% highlight bash %}
  sudo apt-get install logwatch
{% endhighlight %}

Step 5. Check if logwatch works with ssmtp.

{% highlight bash %}
  sudo logwatch --detail high --mailto EMAIL_TO_SEND_TO --range yesterday
{% endhighlight %}

You should now get an email containing yesterdays logs.
Last Step! setup a cron task to send your logs every day.

{% highlight bash %}
  sudo crontab -e
{% endhighlight %}

Your task should look like this:

{% highlight bash %}
  # m h dom mon dow command
  0 1 * * * logwatch --detail high --mailto EMAIL_TO_SEND_TO --range yesterday
{% endhighlight %}

Enjoy!

---
layout: post
title: Afternoon hacking with Phoenix
date: 2016-07-04T09:38:00+00:00
tags:
- Programming
---

Over the past few months my little side projects haven't made it off the starting blocs. They have all had the same downfall of trying to achieve too much.. make a React app using Phoenix as the backend but spend the whole time getting the build tool setup how you want it.

So today instead I decided to keep it simple, create an app to track my climbing sessions. Currently I keep all of my notes in a book which is boring as there isn't much you can do with the data.

As well as keeping my tech aspirations low I think what also really helped was that I defined my goals at the start:

* Quickly record training session.
* Mobile friendly.
* Make data available.

Here it is, the sign in button doesn't toggle and it looks pretty crap but I created something usable in under 4 hours... most of that was rolling my own sign in logic :

![Image of the logs index page](/images/post_images/training_app.png)

My future goals are once I have recorded a few weeks of data I might compare the number of hours training against conditioning and stretching to see if patterns emerge with tweaks/injuries. I could also map it against personal bests to show the effects training had over time.


Checkout the code [here](https://github.com/alex-handley/climbing_training_app)

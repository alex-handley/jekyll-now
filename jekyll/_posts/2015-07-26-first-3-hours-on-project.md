---
layout: post
title: "My first 3 hours on a project"
date: 2015-09-05T09:38:00+00:00
draft: false
tags:
- Programming
---

I recently started a new full time job which is a little daunting, for the first time in a long while I am going to be owning this code not just for 3/6 months but for possibly years to come.
After cloning the codebase, here are some of the things I did:

## Open up the ReadMe

The ReadMe should hopefully tell you everything from the purpose to its dependencies. Sometimes instead of this you find a description of the framework or just placeholders, after much practice I can now not go into full on judgemental mode (this has taken some practice).

![Rails Readme](/images/post_images/rails_default_readme.png)
<caption>The worst case, a default Rails Readme</caption>



## Open the Gemfile

The Gemfile can act as a harder to read Readme when the Readme is missing. For example if you see the PG gem then it is very likely the project is using PostgreSQL for it's database adapter. Hopefully the Gemfile will also contain version numbers as well so you know immediately if Rails is running an ancient vulnerable version.

![Rails ERD](/images/post_images/example_gemfile.png)
<caption>A great Gemfile apart from PG not having a version :)</caption>

## Get someone else to do the leg work

I wrote a <a href="https://github.com/alex-handley/schoolmaster">Gem</a> a while back for the interns at my company, its purpose was to look for the obvious issues that lead to the code style type discussions in a Pull Request.
The gem runs the following tools:

- Brakeman
- bundler-audit
- cane
- Rails Best practices

These tools can give you a rough idea of code quality and if there are any critical issues that need to be solved such as vulnerabilities in gems or SQL injection possibilities.
The warnings are really just a guide but can help you get a grasp of the project.

## Look at the ERD Diagram

I don't always do this but if I want to quickly work out how the database has been designed (or not designed) I install the <a href="https://github.com/voormedia/rails-erd" target="_blank">Rails ERD</a> gem which creates a PDF <a href="https://en.wikipedia.org/wiki/Entity%E2%80%93relationship_model">ERD diagram</a>.

![Rails ERD](/images/post_images/rake_erd.png)
<br />
<caption>PDF output from Rake ERD</caption>

## Take a look at the open Pull Requests

Looking at the open pull requests you can see what the team is working on and get a grasp on how
the teams works. For example:

- PR Labels
- Branch naming
- Do they use hound?
- Is there a CI?

## Fix a bug

The last step is to dive in and try and fix something, when a new developer joins the team I normally try and ensure a minor issue/feature is on the board for them.
This gives the developer a chance to push, get green on CI, create a pull request and then deploy to staging/production.
I think this is the best first day a developer can have, it really breaks the ice and gets them off in the right direction, you can even pair on the bug :)

Hopefully your team is mature enough to not troll the PR!


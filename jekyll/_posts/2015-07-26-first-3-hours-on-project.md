---
layout: post
title: "My first 3 hours on a project"
date: 2015-07-26T09:38:00+00:00
draft: true
tags:
- Programming
- draft
---

I recently started a new full time job which seems a little daulting, for the first time in a long while I am going to be owning this code not just for 3/6 months but for possibly years to come.
When I first pulled the main app I wanted to get to know what I was going to be working on. Here are some of the things I do.

## Open up the ReadMe

The ReadMe should hopefully tell you everything from the dependencies to its purpose. Sometimes instead of this you find a description of the framework or placeholders, after much practice I can now not go into full on judgemential mode straight off (this has taken some practice).

## Open the Gemfile

The gemfile can tell

## Get someone else to do the leg work

I wrote a Gem a while back for the interns at my company, its purpose was to look for the obvious issues that lead to the code style type discussions in a Pull Request.
The gem runs the following tools:

- Brakeman
- bundler-audit
- cane
- Rails Best practices

These tools can give you a rough idea of code quality and if there are any critical issues that need to be solved such as vulerabilities in gems or SQL injection possibilities.
The warning are really just a guide but can help you get a grasp of the project.

## Look at the ERD Diagram

I don't always do this but if I want to quickly work out how the database has been designed (or not designed) I install the <a href="https://github.com/voormedia/rails-erd" target="_blank">Rails ERD</a> gem which creates a PDF ERD diagram.

-- picture of erd

## Take a look at the current Pull Requests

Looking at the current Pull Request you can see what the team is working on and get a grasp on how the teams works e.g.
- PR Labels
- Branch naming
- Do they use hound?
- Is there a CI?

## Take a walk

Next I normally take a code walk around the project to try and familise myself with the app, the best two places are normally config and lib where the dirty secrets are normally hiden,

## Fix a bug

The last step is to dive in and try and fix something, this give you a chance to..


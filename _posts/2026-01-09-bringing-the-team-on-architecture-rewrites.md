---
layout: post
title: Bringing the team on architecture rewrites
---

My what a mouthful and how unhelpfully clear. Let me start with an example - We're rewriting a core part of our sandboxes so we can collapse ~15 discrete sandboxes into one and this type of architecture rewrite is best done with one person driving at all times (a separate argument I admit). It's about to land in trunk, so how can the team best internalize the changes and understand what is about to hit them? They work in these sandboxes everyday so this is a big shift in their daily development flow. When it lands it needs to cause the least amount of disruption possible. 

A sharp edge for the team. I've battled the best way to introduce these types of changes to a team of 5-15 people and have a few solutions. These solutions all center on sharing information as early as possible but they come in widely different formats.

The go-to easiest is some kind of overview document like an ADR or tech spec. These tend to go out of date pretty quick if the type of work is speculative and we don't know quite where we'll end up. However if you have a clear destination they are perfect. I was reflecting on this with a multi team migration I led successfully implemented an architecture I defined from 9 months ago. I went to rewrite a deep dive document and was pleasantly surprised with how much was still accurate in my original document - surprised!

A less appreciated, but quicker and as impactful is a note of "hey I'm gonna look into XYZ". Drop it into a team channel and now the team is aware of where you are spending your time. This is really useful for contributors who need to go on "explore" tickets where they aren't sure how they'll get a conceptual idea accomplished and if this idea is even possible. A simple note and big impact.

I'm a big believer in the rule of three, so if you made it this far I hope you have a good day and I these are the best tools I use. Offer me some other ideas please!

_I didn't use AI for this, but did plug it into a word counter/spell checker. 1 word misspelled_

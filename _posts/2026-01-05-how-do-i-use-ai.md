---
layout: post
title: How do I use AI
---

I've been reflecting on my own use of AI over the past few weeks and want to memorialize how I'm currently thinking about and using it. When the boom started (at some point this past summer) I viewed it how I've viewed most recent booms (NFTs, Crypto, etc...) which is to avoid it. Because I'm not quick enough to pickup on the new trends to benefit monetarily. But AI seems to be sticking around, so once my philistine-based techno philosophy was satisfied I started to poke around and talk to others. I want to say I started as most AI newcomers start by having Chat GPT write some new sentences or words for me. Every once and while there's a cool prompt to use that turns your recent chats into an image (the modern-man's word-cloud). While it definitely can type faster than me - Mavis Beacon with the bugs on the screen still haunts me - it takes my words and pairs them down into nothing better than the next persons. My way of writing becomes diluted by the same slop that everyone else uses. So what benefit am I finding with AI tooling right now? 

## Templated documents 

I have a few PRD (Product Requirement Documents) templates saved in a local folder or in the company Notion which I'll first pop into Chat GPT. I'll copy paste and say something like "this is a template. Use this for the next prompt I give you". Yes, I rarely use proper sentence structure for AI or proper spelling. Then I'll hit the speech-to-text option for Chat GPT and talk for a good 5-10 minutes, end the speech, and add in a "now format this into the template without modifying the speech patterns or content unnecessarily". Sometimes I'll ask for a review as well if it can't understand what I'm saying. Either way, I end up with a quickly written, well structured PRD in a few minutes. I already have most of the info in my head and can easily paste in my source materials as needed.

## New codebases or coding languages

With coding (I'm typically in JS land), I've found the best tool to be Claude Code in VSCode. For a new codebase or language I'll prime it with context and ask for it to first give me an opinion on the current repo and provide a 5 sentence overview and then a deep dive architectural review. This seems to help future prompts (and the 5 sentence blurb helps me quickly find direction). Then I'll plug in the text of the ticket and hit go and manually review all changes it makes. If I disagree on the changes, I point directly to the file and write my opinions into the chat and go back and forth. 
I've found that it is absolutely terrible with heavily styled components as the tool can't see the UI changes or understand the connected nature of multiple components and their individual styling rules. But of course Tailwind is excellent. 

## Slurping up other AI slop into the actual unique idea the person had

In my org I have a few folks outside of my reporting structure that churn out AI documents like you wouldn't believe. I'll copy and paste the entire document into Chat GPT and ask it to distill the document to the original idea or prompt(s) that the user may have used. This gets rid of the AI words (slop) and helps me get to the actual point the person is trying to make. 

_I didn't use AI for this, but did plug it into a word counter/spell checker. Only 10 words completely mispeled._

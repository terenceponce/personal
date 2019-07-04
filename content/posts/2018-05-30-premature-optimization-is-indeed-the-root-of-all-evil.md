---
title: "Premature optimization is, indeed, the root of all evil"
date: 2018-05-30
draft: false
toc: false
images:
tags:
  - personal
  - musings
---

This is a story of how I cycled through various technologies only to end up back where I started.

I’m sure you’ve read a lot of stories about why people shouldn’t be always going for the latest and greatest technology when it comes to creating software. I did, but I realized recently that I didn’t actually learn from these stories.

This whole thing started when I became part of a project that I had a stake in. It’s a mobile/web app that should have a backend for processing data. I was assigned to be in charge of the whole development of the software needed by the owners of the project, so I got started in my favorite programming language — Ruby. The plan was to use React/React Native for the frontend and Ruby on Rails as the backend. We’re not going to talk about React in this blog post. This post is all about the backend.

A lot of people who know me don’t know this, but one of my recent jobs hired me to be the technical lead of a group of Elixir developers. I learned about Elixir/Phoenix and how it’s basically Ruby on Rails that “scales” because of this job. I added the double quotes for scale because while it may be true that Elixir/Phoenix is great for concurrency, it’s not fair to call Ruby on Rails as something that can’t scale. Unfortunately, I was too caught up in the hype to take a moment to think if this statement is actually true.

After acclimating myself to Elixir, I thought about something so wrong that lead me to making my first mistake — “What if the app explodes after it launched? How will it handle the the surge in traffic?”, After that moment, I decided to read up on comparisons between Ruby and Elixir and how Elixir would not have those problems because it can handle millions of concurrent connections.

And so I did. I did the horrible thing and rewrote everything that I did into Elixir. It was great at the start. I fell in love with the way code is written in Elixir compared to Ruby. Eventually, the development slowed down because of my lack of knowledge. I’ve only been writing in Elixir for a few months, so I wasn’t as comfortable with it as I would in Ruby, which I’ve been doing since 2011. The experience became frustrating shortly after a couple of encounters where I was struggling to do something that was supposed to be basic (if I would in Rails) in Elixir.

A lot of those frustrations circled around Ecto, the de-facto database wrapper for Elixir. As a Rubyist, it’s easy to take ActiveRecord for granted, but it’s really hard not to think about ActiveRecord as I was encountering problems with Ecto. I can’t think of specific examples about where I struggled in Ecto since it’s been a while now since I’ve written any Elixir code, but it was frustrating enough to turn me off from it.

Another problem that I encountered with Elixir is the lack of libraries. This problem becomes fewer as time goes on, but it‘s really obvious when you compare it to the vast number of libraries you can find in the Ruby ecosystem. I ended up having to write a lot of code that should’ve been a library integration instead.

This is the second half of the story. One day, I heard from a friend of mine that his company is using Firebase and it worked wonders for them. I’ve been hearing about it for a while, but I never really gave it a fair shake. I played with it for a while and I really liked it. It provided a lot of features that I would have liked as a library in Elixir and it was built for handling a lot of real-time concurrent connections.

I did another big mistake and decided to move to Firebase. Again, it was great at the start. I was able to rewrite the backend really quickly this time and I was able to add some new features. After a while, I realized the issue with going to Firebase — it cost me my sanity. If you’re serious about using Firebase, you would eventually end up using Cloud Functions because it’s the only way for you to write any custom business logic. Firebase can only offer so much out of the box, so you need Cloud Functions to go beyond that.

The downside about Cloud Functions right now is that it only supports NodeJS. Compare that to AWS Lambda that supports a couple more languages. I’m comfortable with Javascript, but I’m not really a NodeJS guy, so I ended up writing a lot of, in my standards, bad code. There’s also the fact that there’s not a lot of things to read outside of Google’s documentation, so it’s basically the Wild West when it comes to how things should be done.

The hard lesson that I realized after all this is that while I’m all for speed of development and insane performance/scalability, it’s all moot when the resulting product is a hot pile of mess (or code, in this instance). It’s easier to make a great Rails codebase scale rather than maintain a fast, but bad Elixir/Firebase codebase. It also doesn’t make sense to worry about scaling for thousands of customers when we haven’t even gotten ONE customer.

I ended up going back to Ruby on Rails and I haven’t been happier. Of course, I’m not saying that Elixir or Firebase is bad. The take away from this post is that you should prioritise developer comfort over some potentially killer feature of another language/platform. It’s just that I have around 7 years of Ruby experience so it makes sense for me to use that to launch a serious project. I would happily give Elixir another try in the future when I’ve gained more knowledge.

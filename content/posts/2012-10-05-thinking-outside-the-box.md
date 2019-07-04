---
title: "Thinking outside the box"
date: 2012-10-05
draft: false
toc: false
images:
tags:
  - personal
  - musings
---

One of the hardest things to do is to think outside the box.

The fact that you have to completely disregard what you've already thought and think of something new and different than the previous one is one of the most frustrating experiences that happen frequently in a programmer's life.

Let me tell you about something that happened to me a few weeks ago.

In one of my client projects, a Twitter application, the client asked me to have the application update the existing Twitter users' information every now and then, so we would have the latest info on them.

The first thing I thought of doing was to write a Rake task and use it with Cron. Actually, I think that's the only option for doing that specific task.

I know the tools that I should use, how do I actually do it? The two glaring limitations I could see at that point was that Twitter only allows up to 350 API calls per hour which means I can't update all of the users at once and that I would have to do it by batch.

Second, there is no way to have Rake automagically do a task on a series of data by batch. Despite that, I was still dead set on finding a way to have Rake automagically do it.

After a couple of hours of not being able to find anything, I realized that I need to take a step back and think of a creative way to go about the limitations. Then, it hit me. The solution was actually very simple.

I just have to add a new boolean field that tells if it had been updated recently and have Rake check for records that haven't been updated through that boolean field. If the record has the field as true, it will ignore it and just find the rest of the records that have the field as false.

After a week, I would just have Cron update all of the records as false again to tell Rake that it needs to be updated again. I'm pretty sure this is hard to understand since I'm not showing any code, but use your imagination!

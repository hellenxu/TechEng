### What's new in Android Studio (Android Dev Summit 2019)

Welcome to our session on what's new in Android Studio. Today we're going to talk about all the latest features inside Android Studio, plus take a deep dive into something you heard about during the Android Dev Summit keynotes. As a framework session today, we talk about features that available in stable today, all the way to the features you find in Canary channel. To get us up to speed, let's take a quick step back. 

At Google IO, we launched Android Studio 3.5 into Beta. There we focus on system health, feature polish and bug backlog. Just this past month, we launched Android Studio 3.6 into the Beta channel. That has things like ViewBinding support, emulator with integrated Google Maps, design tool improvements, and Kotlin&JNI support. And today, we're excited to talk about Android Studio 4.0.

Ok, with this context, let's talk about what's inside of a stable channel today.
To recap, Android Studio 3.5 focused on three main pillars today:
 
1) System Health
2) Feature Polish
3) Bug Backlog

Now, that all culminated in something we call 'Project Marble'. Specifically, Project Marble includes a number of small projects that our team worked on across the board. Things like crashes, improve apply changes, hangs, CPU usage for the emulator, R8 improvements and our bug backlog.

Project Marble

1) Apply Changes
2) Project Syncing
3) Memory Sizing & Leak Fixing
4) Project Upgrades
5) IDE Hangs
6) Muting Exception Reporting
7) Optimize IDE Plugin Usage
8) Emulator CPU & Memory
9) Build Output Window
10) Microsoft Windows Support
11) Data Binding
12) Finish R8
13) Faster Build Speed
14) Bug backlog

Now, I want to talk about some of the improvements, but also the successes of Project Marble for your awareness. One of the issues we talked about and worked on was around hangs and latency. For this specific example on the screen, we looked at xml typing and data binding. What we found is there are some critical bottlenecks we had in typing. So we analyzed and improved those things. We did that not only in xml, we did that for Java and Kotlin programing languages as well. 

Another pain point we heard from you is about memory. And the symptom of this is that, hey, the ID is slow and not responsive. And the root cause of that is actually memory issue itself. So the first thing we did is actually to create a new set of infrastructure and dashboards to catch these issues as we checked in codes into Android Studio. Of course, we can't catch everything, so then we added a new set of client side analysis for you. So if you notice if Android Studio is slower and sluggish, you can now create your own heap dump and have a sanitized version of heap dump you can share with us, so we can understand what's going on with you machine and how to best solve these problems.

Then lastly we heard a lot of people complain about sort of the overall speed of Android Studio. What we found from the opt-in data is that a lot of people were using the default settings of Android Studio. Well, this is fine for interim projects and beginning projects, but as your project expands and you add more functionality, you can actually increase your heap size for better performance. So if you ever see this pop-up, many come up in the bottom right-hand corner of Android Studio, please look at that and accept those settings. We've actually applied some analysis and some models to understand how to best configure your Android Studio and your Gradle plugin to have better performance. So speaking of the build speeds, we spent a lot of time during Project Marble to look at how to improve build speeds. So before Project Marble, we had about a 12.5 second average build speed. And we've drilled that down to about 9.5 seconds post-Project Marble. Digging in a little bit into the data you saw this morning, specifically, we're actually working on build speeds along the way from 3.0 to 3.3. What was happening, we noticed is that projects are just becoming more complex. Either if you added annotation processors or more features to your build speeds, it was just slowing down and you weren't able to keep up. So in 3.3, 3.4 time frame, we now were able to stabilize that. And what we're excited about is with Android Studio Gradle plugin 3.5, we actually have seen a decrease in build speeds.

And the last we looked at for us is on deployment speed. So deployment speed is the time it takes for you to make a change on Android Studio and see it on a running device or an emulator. So the medium before that, before Project Marble, was about 7.9 seconds and we drove that down to about 3.6 seconds. And that was all around the feature called Apply Changes. And Apply Changes, if you don't know, is a replacement to Instant Run. And Apply Changes is a fast, iterative way to make changes to your application. So if you haven't been using Apply Changes, I highly encourage you to use that as well because it will dramatically improve your incremental change and workflow in developing with your app in Android Studio. 

Alright, so recap, we've done a number of improvements for Project Marble and we're really excited about those things. And thank you to all of you who filed bugs and shared with us those opt-in data. Without that information, we wouldn't know what to prioritize to make things better for your development. 

But quality is definitely not done. We set a new quality bar for ourselves that we want to maintain going forward. So with this quality bar set forward for ourselves, we want to continue working on new features for Android Studio. So today we're giving you a preview of a lot of the new things we're working on inside of Android Studio. But before we migrate these features from Canary to Stable, we're going to prioritize and assess those features and match them to the quality bar that we set during Project Marble. So instead of talking about all those cool features we added to Android Studio, I'm going to have Tor walk us through some of the cool things that we have inside of Android Studio.

So one thing we hear over and over again is that you want your builds to run faster. And as you just saw, we've been making some good progress. But as you'll see shortly, just speeding up the Android plugin is only part of the puzzle. So when you want to write your apps and make them faster, you run a profiler, right? Well, what we've been working on is a profiler for your builds. So I'm going to show you what that looks like. So this is the Santa Tracker Project with some deliberate build mistakes I've added in. And after you've built now, you get this new build speed task, one for each build that you run. And this gives me a breakdown into what happened during the build. So for example, here, I have a breakdown of all the tasks that determined the build duration. So this is showing me the tasks are on the critical path. There's no point in trying to optimize a task that is not on the critical path. You make it faster, you're still waiting for a longer task. Right? We also give you a breakdown of the build time for each plugin. So you can see here, I have a plugin that's taking up 40% of this build. This happens to be a third party license plugin, so what it's doing is very valuable. It's fetching license files over the network and inserting it into the Help menu. But maybe, I don't need to do that on every single full debug build, right? So I can maybe investigate and get into release builds. So this gives me an insight that I can then go and do something about. We also tried to surface a bunch of problems in the build. So for example, always run tasks. Tasks should only run when their inputs have changed, but as you can see, these tasks are not actually declaring their inputs. And that means they're going to have to run every single time. So if this is your task, then you know you should fix it. Otherwise, you should contact the author and ask them to fix it. 

Another problem that we pinpoint for you here is task setup issues. So if you configure multiple tasks to share the same output directory, then they can't run incrementally. So if you do that, you're breaking incremental compilation, and again, we're surfacing that to you right here, listing the tasks that are conflicting. Non-cacheable tasks. This is pretty important if you want to use the distributed cache. These are not going to work. And then, of course, non-incremental annotation processors. So if you use an annotation processor that's not incremental-aware, it'll force a full rebuild. And they all have to be incremental-aware. Now, we've been working on a lot of them and working with annotation processor authors to actually update to Gradle support for incremental APIs, and so in some cases, it's as easy for you as updating to the latest version. So when we know, for example, here, we're telling you auto-value, switch to version 1.6.4 and your build will run faster.

And then there's configuration time. So configuration is where tasks are created, whether or not they're going to run. This is supposed to be a very fast operation.
But some plugins will actually do a lot of computation during configuration. I've actually created a slow one here that waits for two seconds. It's simulating, doing lots of computation that you shouldn't, but this gives you a chance to sort of dig in and see exactly what's going wrong. So as I mentioned, you get one Build Speed tab
each time you invoke the build. So I did a full build here for about three minutes, two three minutes. I then did an incremental build. So now, I can see the breakdown of what happens in my incremental build. You can see the licensing is no longer the issue here. And so, by breaking this up for each build, you can decide whether you want to optimize for CI, the full build scenario, or if you want to optimize for your incremental build in the IDE or preferably both. But you're probably going to have more value out of the incremental builds as you do those over and over and over again. So that's the new build attribution feature that we have in 4.0. It's not actually in Canary 1, but hopefully, we will land it soon.

So while we're on the topic of Gradle, we now support the Kotlin Gradle DSL. So that means that you can write your build files in Kotlin. And that's really good for the IDE
because we can reason about Kotlin. It's statically typed, so the editor support is better. And so, for example, code completion now, if you've ever wondered what you're
allowed to configure inside default config, well, wonder no more. You can see the exact list here. Similarly, lint options. If you want to know what you can invoke and what you can customize, they're listed here. And you can also pull up docs for the DSL, which is pretty handy, right from the IDE.

We also run lint on your KKS files. And in 4.0, the biggest amount of work for us was to implement the ability to read, analyze, and write these build files. So the product structure dialog now also handles Kotlin DSL. So I can go in here and I can accept these suggestions to update to the latest version of these libraries, for example. And then hit Update, and then it's going to rewrite the build file and run another sync.
OK.
Let's see.


#### Reference
[Youtube video](https://youtu.be/XPMrnR1_Biw)
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




#### Reference
[Youtube video](https://youtu.be/XPMrnR1_Biw)
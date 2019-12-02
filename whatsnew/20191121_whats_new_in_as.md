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


#### Reference
[Youtube video](https://youtu.be/XPMrnR1_Biw)
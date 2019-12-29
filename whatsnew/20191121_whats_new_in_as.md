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

Let's go to number two.
So let's take a look at the Compile Options section.
You'll see this new flag here called coreLibraryDesugaringEnabled. So this is for a new feature in 4.0 called library desugaring, and this lets you access APIs that normally require a higher API level on an older API level. What we do is we rewrite those calls into a backboard library that we bundle with your app. So let's take a look at what that looks like. So here, I'm using Java.util.stream, lint tells me that this is going to crash prior to API level 24 so you can't use it. Right? Well, now, if you turn on library desugaring, it's actually complaining in this module because this code isn't in the module where I turn on library desugaring, but if I switch to the same code in my library desugaring module, you can see lint is happy because this is going to work. And let me show you the bytecode to see how this works. So this is the same method I just showed you, and if we look at the bytecode, you can see that java.util.stream has been replaced with this j$util.stream. That's basically the backboard library. And it's good to be aware of this because you might see these package names in stack traces and debugger and so on. And note that this is not limited to java.util.stream. You can use this for java.time. You can use it for java.util.function, java.util.optional, and we even rewrite references to concurrent hash map. Because it turns out that, on some devices, concurrent hash map has a really bad bug. And so by using this feature, we will just replace your concurrent hash map usages with the safe one and you won't be bit by that bug. All right. Speaking of decks and R8s, for code shrinking, we still use ProGuard files to drive the process. And so in 4.0, we've improved our support for ProGuard files. We have better code completion, we have go-to declarations, and the most important part is that it is now find usages and refactoring aware. So if I, for example, rename this class that happened to be referenced from a ProGuard file and I apply that refactoring, you can see that the ProGuard file is also updated. So no more stale ProGuard files after this.

So back to our Gradle file again. I've also turned on view binding. So view binding is very much like data binding, but it is easier to use and it has a smaller impact
on your build speed. So once you turn this on, it'll create a binding class for each one of your layouts. So here's a class that first does things the classic way, right? So we have a set content view, we'll be passing in a layout resource, then we fish around in the view hierarchy with these find methods. We apply a cast that'll hopefully work, and then we take our chances accessing that object. With view binding, what we do instead is we access or inflate the layout with this generated binding class. And now, we get this binding instance which knows exactly which views are present in the layout. So in this case, text view is present in all versions of activity main. So that means that we know it's non-nullable and it's safe to access. I will have no null pointer exceptions with this one. View binding knows that button is actually missing from one of the layouts. So it's marked as nullable. And if I hadn't used the elvis operator here, Kotlin would have refused to compile. So view binding is great for catching nullness, and it's also pretty good performance.
All right. So view binding makes it really easy to access and update your views, but we're working on a brand new framework to make it even more powerful and easy. And that's Jetpack Compose. So this is Hello World from Compose. So what we do here is we can take an existing composable, or a widget, and we can wrap it in our own function and customize it with some arguments. And as soon as we put a composable annotation on it, voila, we've created our own widget. And we can also market with this preview annotation. And then it shows up in a preview pane inside the Kotlin file. This is a bit like our sample data support in the old Layout Editor, but now applied for Compose. And so we've created our own widget called Greeting here. We can take that further and we can throw a for-loop around it and other conditionals. And we can wrap it inside another composable. So you compose new widgets by adding composables. So the name makes a lot of sense. So we had the preview again, and note that this preview is not limited to one. So here, I have a button widget that I've created. It has two states, on and off. And notice how I can preview all the states, including in dark mode as well, which is pretty handy.


And here's an even more complex examplefrom our Newsreader app.
So this is a card we used to show available stories.
And here, you can see we're also showing you,
for example, what this looks like with a larger system font.
So the embedded preview is really
great for tweaking your views as well as seeing what they'll
look like across different dimensions like theme and font
size and things like that.
But sometimes, you really want to run the app
to modify the behavior.
And so I'm very excited about this next feature.
If you watched the keynote, you've already seen it,
but I'm still excited about it.
This is a feature I've wanted since before Studio 1.0
but it was very technically challenging,
but the team is in the process of pulling it off.
And that is, of course, the ability
to have the emulator embedded inside the IDE.
Yeah, I love this.
[APPLAUSE]
So this is the real emulator, right?
It's not just simulating the app in isolation.
I can install accounts, I can launch intents and all that.
And what's nice about having it inside the IDE
is that it participates in window management, right?
So if I'm the source editor and I want to focus,
I press a key binding, I'm in my editor,
and I can bring it back very easily.
So it's super convenient.
So this is my Compose app running.
We can drill into it here.
And let's click on the Bookmark icon.
And when I do that, I'm launching
an attempt and that's actually not what I wanted to do.
So I've introduced a bug in the app.
So let's just go and fix that.
So I'll jump to the related code.
This is the toggle bookmark code.
Oh yeah, I had a to-do here and a stopship marker, too.
Let's try Apply Changes.
And it's now reloaded the app, and I can invoke the bookmark
and it works.
So this combination is pretty productive right here,
running in the IDE.
One last thing I'll mention about our Compose support
is we're working pretty hard on making the editor
experience better, right?
So for code completion, for example,
we are ranking composables to the top.
Notice that we're also-- normally,
you'll have types on the right, but we are removing types
from the right so we have more space
to show you all the parameters.
We're hiding defaults.
And we also have a special code completion insert handler.
So if I, for example, insert a row,
if I don't enter anything as a parameter and I hit Enter,
we remove the parameter list, and so on.
So we're trying to make this editor really smooth
as you're working on Compose.
OK.
We're also working on the classic view system.
So let me switch over to the Layout Editor.
It's the Sunflower app.
So here's an XML file.
Now, do you remember how, in the Compose file,
we included the previewer right there in the source editor?
We've done the same thing for layout XML files.
We used to have a separate tool window,
and in the Layout Editor, we had Design and Source tabs.
What we're doing now is, instead, we
have these three buttons at the top.
This works in Compose.
It works in the Layout Editor.
And you can use key bindings to quickly map between them.
So if I press Control-Shift-Left here, I'm in pure source.
If I go to the middle one, I can have them side by side.
And if I go all the way to the right,
I have the full Layout Editor.
And this also works for custom views.
So here, I have an Android custom view,
and I now, within the Java or Kotlin editor,
I can see my custom view.
When I'm tweaking the [INAUDIBLE] code,
it's right there.
And it makes it a lot easier to work with.
[APPLAUSE]
Right.
So let's look at the Layout Inspector.
So I'm going to run this app again.
Open up the emulator.
All right.
So this is the Layout Inspector.
And actually, that's not.
That's the app running.
This is the Layout Inspector.
And it's going to attach to my running app.
See, OK, it's already connecting.
So what it's doing now, it's going to pull down
the whole view hierarchy.
There it is.
Let's give ourselves some more space.
So the first thing I can do here is I can click on various views
and I can see their values.
We had that in the old Layout Inspector.
But what's new is this Declared Attributes section.
So here, you can see resources that
are coming from binary resources or XML resources.
So let's take a look at the label, Add Plant.
Where's that coming from?
Well, I can expand that and I can
see that it was actually specified
right there in the Layout file.
And I can continue with the whole resolution stack
and see the exact line and language that it came from.
And normally, it's not difficult to find
where a string came from.
You can just search for it.
But what about colors?
So you can see that this button has a green color.
Where's is that coming from?
And again, I can expand and you can
see we have multiple alternatives here.
And it's probably too small for you to see.
I mean, I can barely see it from here.
But there's strikethrough on all the theme references
for material where this color would normally
be specified as gray.
Instead, we have some other attributes
that have a higher priority that are winning,
so I can see that the real green is
coming from this local layout reference
to a theme attribute, which again, we
can chase down the values for.
So this is a bit like the Chrome DevTools,
where you can sort of investigate
the whole resolution to figure out why, oh why is this text,
whatever font or color you're concerned about.
One other thing to point out is that this inspector is not just
a static dump of the view hierarchy.
It is optionally live.
So if I go up, and in my running emulator, click, for example,
on the Plant list, you can see that it's now
going to fetch all the surfaces and stay up to date.
So I can continue to see what's happening in the app.
And one really nice thing about that
is that I can now drag this in 3D
and see the full view stack here.
[APPLAUSE]
And the really useful thing is, look,
we have a full texture sitting back here.
And notice how we're showing textures
that are hidden, right?
So this is not just a screen grab from the device.
We're getting all the surfaces because this
is running on Android 10.
So we had to actually add some support
to do the value tracking and this bitmap fetching.
But now, we can see that we have this texture hidden
by the screen.
So this is overdraw, this is bad,
and this tool makes it easy to track down
those kinds of bugs as well as issues with clipping.
So that's the Layout Inspector.
All right.
Let's talk about the Motion Editor.
All right.
So here, I've created a very simple constraint layout.
And what I want to do is I want to animate this
so that it's a bit more lively when this layout shows up.
So what I do is I open up Convert to Motion Layout.
This will migrate my layout into a motion layout,
plus create a scene file that's going
to store all the key frames that we're about to create.
So the first thing I can do is to select the start state.
So we have start and end, right?
I'll select a start state, and then I'll
start changing some of the constraints in the scene here.
So I'm going to be deleting this to move the text to the top.
Let's also attach this button to the bottom.
And now, we've already created ourselves an animation.
So if I switch to the transition view and I hit play,
you can see it's now going to start animating this.
And we can go ahead and turn on showing paths
so you can sort of see these lines here
showing how these widgets are going to move.
So the next thing I can do is I can
try to make this motion a bit more interesting.
So I don't want a linear path for the label.
So I'm going to now add a key frame,
a position-based key frame, on the text view.
And I'm going to have the type be relative to parent.
And let's move it about 100%.
I just learned, by the way, that in Android, we
use one-based percent, which doesn't make a lot of sense.
All right.
So you can see now that the motion is a little bit more
interesting, right?
We can also fade in the button from the bottom.
So what I do then is I add a key frame based on an attribute.
So we'll pick the alpha and we'll put this on the button.
So now, this is going to fade in from about 0.5,
and we can change that to be--
let's start from completely transparent instead.
So now, this button is going to fade in.
And let's add some rotations because I like rotations.
So another key attribute.
And this time, we're going to be rotating the text view.
So rotation on alpha.
It starts out at 45 degrees.
I like to give it a bit more spin
because rotations are cool.
So let's do 145.
And we should also move it off screen
so it doesn't start in the middle.
And so to do that, I will first select the States, Text View,
and then we'll add bottom padding here.
Let's say 300 or so.
All right, let's see how this is starting to look.
So when I play, OK, we got some motion.
[APPLAUSE]
Yeah.
So you can see that you can really go overboard.
Do you remember the web in the old days
with the spinning logos on the web page?
Yeah.
Don't go too far.
But with some tasteful design, you
can really accomplish a lot with this tool.
So let me switch to a slightly more complex layout.
So what you see here is just start and end states, right?
Well, that's just the default.
You can also create many other states.
So here, I have a base state.
This is just a bunch of cards laid out with different colors.
I have this other state where all the cards are
on top of each other, and then there's
a third state where they're all sort of off screen
up to the left.
And now you can sort of sequence the transition
between all these different states exactly how you
want them.
So we can have this transition.
We can have this transition.
We can have-- let's see.
I chose a state.
We can have this transition.
And let's check out the translation
curves down here, too.
So you can do really advanced things with this tool.
The last thing I want to highlight
is that you can easily also create click and swipe
handlers.
So when you do that, Motion Layout
is going to add the code for you to basically add listeners.
So if I had a swipe handler, for example, the user can swipe
and then Motion Layout will scrub
through the animation, a bit like the Quick Settings
pull-down on Android.
You can do this through the tool,
which is pretty fantastic.
There is so much to show here that I don't have time
to get into.
But the good news is, if you stay
in your seats for two or three more hours,
there's a whole talk from the Design Tools team
that are going to show you through all the goodies
that I couldn't show you today.
All right.
So a couple more quick things to show you.
First of all, in the Resource Manager,
we've updated the Vector Asset Wizard with brand new clipart.
So the Materials Design website has many icons.
We've added them.
More importantly, we now support all the different types.
So you can switch from, for example, filled icons
to outline icons to even the two-tone icons.
So hopefully, you'll find that useful.
Another thing we recently did for editing
is we ported all of our Android live templates to Kotlin.
So if I, for example, want to add a logging
tag into this class, I just type logt, hit Tab.
This will create a logging tag for me
based on the current file name.
Right?
And more importantly, or more awesomely, if you ask me, logm.
This one will basically log the current method name
with all the parameters.
Right?
[APPLAUSE]
And if you want a block structuring comment
in between SPC tab, you can type in your comment, whatever.
So what you want to do is open up the Live Templates
UI, Live Templates Settings, and browse through all of these
to learn what tags you can type in with tab.
It'll save you a lot of time, I think.
All right.
Next, I'm going to show you some stuff in the emulator.
So look, still also runs standalone.
So if I pull up the options, Android now
supports multiple displays.
And so we've made it really easy to test multiple displays
in the emulator.
All I have to do is go in and add additional displays.
I can tweak what kinds of density I want.
So it's suggesting this layout right here.
If I hit Apply Changes now, you can
see I have my emulator connected to multiple displays
that I can then start interacting with.
Yeah.
So definitely give that a try.
All right.
We've also made it easier to test locations.
So let's say I want to test motion.
Let's say I have some sort of fitness app or whatever.
What it can do is say, I want to navigate to the Googleplex.
And I'm going to start from where
we are right now, which is Google Building MP7, I think.
I can now save this as a route.
I'll call it Demo.
Save.
And now, I can play this route.
And let's see what happens if I go into Google Maps
here on my emulator.
If I click the Where Am I icon, it's
now supposed to be showing us that we're moving.
And we are.
[APPLAUSE]
All right.
Unless I forgot something, I think that's my demo.
So now, I'm going to turn it back over to Jamal,
and I hope you enjoy Studio 4.0.
[APPLAUSE]
JAMAL EASON: All right, so some really cool demos.
Unfortunately, we don't have time to demo everything,
so I'll spend a few moments talking
about some additional features we're launching inside
of Android Studio.
So first, we're updating dynamic features.
So as you know, today, with dynamic features,
I can have a module feature connected to the main module.
What we're updating is that now, you can have feature
to feature dependency.
So let's say I have a camera module
and I want to add a video module to that.
So now, I have a direct dependency to the camera module
without having a direct dependency on the app module.
So that's a cool feature we've added for dynamic features.
Another thing that we added behind the scenes
is actually improvements to the Layer Editor.
So actually, what we did, we took the rendering libraries
that we have on the devices and put them directly in the IDE.
So things like Skia and ICU are now
available inside of Android Studio.
And what that enables is two important things.
One is the performance.
So all the demos you saw Tor using
with reactive performance, that's there.
And two, having higher fidelity accuracy.
So specifically, things like improving text justification
mode is now supported.
Having detailed shadows or having 3D perspective.
Or having text path rendering are now
available inside the Layout Editor.
So this is all happening behind the scenes
and helping you have a better experience
inside of Android Studio.
Just as a quick update, we also did a big pass update
on our templates for fragments.
So we have a new wizard that's there.
We updated all the templates for fragments.
So if you use fragments, check that out as well
inside of Android Studio.
Now, a big update for the Android Emulator
is something we're calling kind of this dual ARM x86 support.
So let's say, if you have any C++ code today that had ARM
support only, you weren't able to use the x86 emulator
because that didn't support the fast experience.
And so today, we have a special version
of the emulator that supports both ARM APIs and x86.
So it can run that Legacy or C++ code on one emulator and have
that fast x86 experience.
So we just released this on one version,
and we're really looking for your early feedback
to make sure we have all the right use cases.
And we'll scale this out as you add more features
to this going forward.
And I also have an update on Chrome OS.
So at I/O, we talked about having
full first-class support for Chrome OS for Android Studio.
And one pain point for that is that, if you wanted
to debug your app or deploy your app,
it required you to have an external Android device
or boots your Chrome OS device into the developer mode.
Now, that's no longer needed.
You can do all your end-to-end development right inside
of your Chrome OS device.
So with the updating release of M80 of Chrome OS,
you can now do your development, debugging,
and deploy the app right directly into Chrome OS
to see it running inside of the Chrome OS laptop.
[APPLAUSE]
OK.
So we talked about a range of features that we're releasing.
Things in the stable channel and also
things on the bleeding edge features on the Canary release
channel.
Now, let me talk about the Canary release channel.
Of course, it is bleeding edge, right?
A lot of things are just in development
and we're trying to explore things that are going on.
And to that end, it's actually very lightly tested.
So we do recommend running both stable version of Android
Studio and the Canary version of Android Studio.
And you can do that as long as you
have your Gradle plugin tagged with the stable version
of Android Studio.
You can run both in parallel on the same project
with no issues.
TOR NORBYE: But for build attribution, you'll need 4.0.
JAMAL EASON: Yes.
[INAUDIBLE]
But as far as feedback goes, we do
encourage you to use Canary because it's the best feedback
channel for us.
If you file a issue or a bug on stable,
it's a little too late for us before we
moved onto the next version.
So if you like the features you saw today
and want to give us feedback, please use the Canary channel
for that feedback mechanism.
But even more importantly for us,
we're actually very quality-driven.
Going back to my earlier point about Project Marble,
we set a new bar for ourselves for quality.
So some of the things you might see in Canary
may not make it into the initial version of stable
because it may not be ready.
And that's OK because we want to make
sure it's stable and has a high-quality release
before we make it available to you in the stable release
version.
OK.
So we talked about a range of things today,
and thinking about a recap, we talked about some things
that are actually in Android Studio 3.6,
things like the multi-display mode, Design Tools
updates, and the Intellij update for 2019.2.
And then things like the Jetpack Compose and Motion Editor
support, those are inside of Android Studio 4.0, which is
currently available in Canary.
So please check those out as you're
trying out Android Studio. And with that, please download Android Studio Canary to try these features out.
Again, I'm Jamal and this is Tor,
and thanks for coming to our session.


#### Reference
[Youtube video](https://youtu.be/XPMrnR1_Biw)
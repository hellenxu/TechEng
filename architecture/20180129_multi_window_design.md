### Designing for Split-Screen Multi-Window

#### Content
So there I was building a great responsive UI when it hit me Multi-Window is gonna change everything. Well, okay, maybe not everything, but there are some special considerations when it comes to buiding and designing for split-screen Multi-Window. One thing that has not changed is the importance of a responsive UI. A responsive UI is one that is able to adapt to space available. So some of the same techniques you use before just become even more important when users can resize your app. Checkout our other video on basics of building a responsive UI and the patterns you can use to adapt your layout to change in size. Thankfully building a responsive app for your existing users covers almost all of the configurations you will see in split-screen Multi-Window. But there are two relatively unique configurations you might consider when designing your app. 

1. The first is the 16:9 layout on mobile devices in portrait. 
Here it is important to keep in mind that heavily constrainted vertical space. Make sure users can actually see some content between fixed navigation elements, like an expanded app bar and bottom nav bar.

2. The other is 34.15% layout on tablets. 
Here you have an extremely wide aspect ratio on portrait devices. A technique we have found useful is to start with your mobile device layout and stretch them to fit this new configuration.

Once you have a fully responsive UI, now, comes the fun part, taking advantage of Multi-Window. By default, Multi-Window is enabled and it's enabled for your entire task stack. It means if your root Activity supports Multi-Window, every Activity started from the root Activity also needs to support Multi-Window. This also applies to any exported Activities you have. They will inherit the Multi-Window capabilities of the other app and therefore should always support Multi-Window.

Now, when in Multi_Window mode, the minimum size is 220dp by 220dp. You can use minHeight and mindWidth to set a larger minimum size, but in split-screen Multi-Window, users can still resize it smaller. You will just get cropped off and no one likes an inaccessible button. So try removing as much as possible to get down to that minimum size.

Now, beyond the first priority of, does my app run in Multi-Window mode, it should come as no surprise that the best experience here for users is the fast, smooth transition from the biggest screen size down to the smallest, and vice versa. If your tablet and mobile layouts are very similar, you may find this to already be the case. The important thing to remember is that you don't want the user to feel lost when they resize your app. If your layouts are vastly different, don't change between them. Instead, try scaling your tablet UI down to fit the smallest space. That way, your app will be consistent on all sizes. 

Keep in mind that just like any other configuration change, the default behavior is to kill and recreate your activity when it's resized, reloading all of the resources for you. Assuming you save your state using onSaveInstanceState() and use loaders or other techniques to keep data across configuration changes, it should be really fast. However, if you want to take total control and reload the only exact things you need, you can handle configration changes yourself by adding the configChanges attribute to your activity and then applying the changes in onConfigurationChanged(). Not for the faint of heart though. Test thoroughly.

Now, if you are a fully immersive experience like a game, I get it, maybe you want to disable Multi-Window. If you aren't targeting API 24, adding android:orientation or android:immersive to Manifest will disable Multi-Window. And of course, the new resizeableActivity attribute can be added to your application or to your root Activity to opt out of Multi-Window. Check out the blog post linked in the description for all the details and some other patterns like relying on edge swipes, should be avoided to best support Multi-Window. 


#### References
[Youtube Video](https://www.youtube.com/watch?v=hi5R0gq9tdA&list=PLWz5rJ2EKKc-lJo_RGGXL2Psr8vVCTWjM)

[Sample](https://goo.gl/lfBcgI)
### Adding Picture in Picture to Your App

#### Content
With Picture in Picture, you can overlay other apps where you users are doing critical tasks, such as watching a video or navigating. It's available whenever your app is running Oreo or above. Let's dive in on how to add it to your app. A Picture in Picture overlay is a mode you enter from a visible activity. The user will see the content of the activity during the entire transition animation, so you want to optimize this experience. Of course, it does count as a configuration change. To avoid visual hiccups, make sure you handle configuration changes in your activity.
```xml
//AndroidManifest.xml
<activity 
	android:name=".MyActivity"
	android:configChanges="
		screenSize|
		smallestScreenSize|
		screenLayout|
		orientation"
	android:supportPictureInPicture="true">	
</activity>
```
How do you design for PIP? Well, designing for Picture in Picture, or PIP, it is simple. It's a small window, so you simply hide all of the views aren't relevant to the overlay. For example, if you're playing a video, you should only show the video content and hide all of the other views to create a simple overlay. Another important thing to know when designing for PIP, overlays have a fixed aspect ratio you configure in code. This let you specify the perfect aspect ratio for your content. Now to support user interaction with your PIP overlay, you may supply a small number of controls called remote actions. You can use remote actions for simple interaction, such as playing, or pausing a video. When in PIP mode, your activity wouldn't get any touch events, instead, you configure remote actions with PendingIntents. When a user presses a remote action, the PendingIntent will be fired, and you'll be notified via a BroadcastReceiver or other means. When your user expends the overlay back to a regular activity, you will get notified via a callback. You will need to reshow all of the hiden views when your activity exists PIP mode, and there is one more detail to know. When an Activity enters PIP mode, it starts a new task without a back stack; when it exists PIP mode, it does not bring back the original task. So by default, Back will leave your app. Your users' navigation history is lost. To recover your back stack, check out our blog post on recovering navigation state. 

```java
// MyActivity.kt

// create a remote action
val pendingIntent = PendingIntent.getBroadcast(activity, 
ACTION_REQUEST_CODE, Intent(REMOTE_ACTION_PRESSED), 0)
val remoteAction = RemoteAction(/* ... */,
	pendingIntent)

// register for broadcasts from actions
activity.registerReceiver(MyReceiver(), IntentFilter(REMOTE_ACTION_PRESSED))

// handle custom actions here
class MyReceiver : BroadcastReceiver() {
	...
}

```

Picture in Picture is a great way to continue your users' critical tasks as they use their phones. I've just covered the essentials here. To learn more, check out the blog post and documentation on Picture in Picture, linked in the description.


#### References
[Youtube Video](https://www.youtube.com/watch?v=hBPd2q2dmXY)
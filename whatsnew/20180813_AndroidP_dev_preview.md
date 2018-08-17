### What's new in the Android P Developer Preview

#### Summary
In the early days of Android, we made the first SDK available as a preview to developers. The vibrate ecosystem and open platform have been important keys to our success, which is why I am excited that we are once again giving developers a very early preview of some of what's in store in the upcoming version of Android -- Android P. Your early feedback is crucial to our efforts to push the boundries of mobile development.

#### JobScheduler Network Updates
In Android P, JobScheduler has been improved to let it better handle network-related jobs. Jobs can now declare their estimated data size, signal prefetching and specify detailed network requirements. For example, when a network is reported by the carrier as congested, JobScheduler might defer large network request while it can take advantage of surplus meter capacity to run a prefetch job that returns content that will substantially improve the app's user experience, such as prefetching top headlines. 

```kotlin
val netRequest = NetworkRequest.Builder()
	.addCapability(NET_CAPABILITY_INTERNET)
	.addCapability(NET_CAPABILITY_VALIDATED)
	.removeCapability(NET_CAPABILITY_NOT_VPN)
	.addCapability(NET_CAPABILITY_NOT_METERED)
	.build()

val jBld = JobInfo.Builder(jobId, serviceComponent)
	.setMinimumLatency(1000L)
	.setRequiredNetwork(netRequest)
	.setEstimatedNetworkBytes
		(DataUnit.MEBIBYTES.toBytes(1))
	.setIsPrefetch(true)
``` 

#### Improving App Performance
Android P brings performance and efficiency improvements to ART, Android's runtime. We've expanded ART's use of execution profiles to optimize apps and reduce the in-memory footprint of compiled app code. ART now uses profile information for on-device rewriting of DEX files with reductions of 11% across a range of popular apps. These correlate closely with reductions in system DEX memory usage and faster startup times.

#### Kotlin
Kotlin is seeing massive growth in the developer community. We've heard so much Kotlin love from developers, and we're deepening our Kotlin investments. In P, you'll see the first results of our platform work with new optimization in ART. Apps written with Kotlin will run faster on P than on previous versions of Android. We're continuing to work with JetBrains to optimize the Kotlin compiler. And we're also improving our brand new D8 dexer to make sure generated code from Kotlin can run as fast as possible. Also, make sure to check out our ongoing preview of Android KTX, which is a set of extensions designed to make Android Kotlin development even more concise and pleasant. 

#### Introducing ImageDecoder
Android introduces an easier way to decode images to either Bitmaps or Drawables with ImageDecoder, which replaces BitmapFactory. ImageDecoder lets you create a bitmap or drawable from a ByteBuffer, file or URL. It offers several advantages over BitmapFactory including support for exact scaling, single-step decoding to hardware memory, support for post-processing decode and decoding of animated images. ImageDecoder allows you to supply a callback, which is called after the header is decoded you can alter the output rather than having it decode the header twice. Decode Drawable allows you to decode drawbles directly. If the encoded image is an animated GIF or WebP, the drawable will be an instance of the new animated drawable.

```Kotlin
@Throws(IOException::class)
fun getPostProcessedDrawableSample(file: File, width: Int, height: Int): Drawable {
	val src = ImageDecoder.createSource(file)

	return ImageDecoder.decodeDrawable(src) { decoder, info, _ ->
		val imageSize = info.size
		val sampleSize = Math.min(imageSize.height/height, imageSize.width/width)
		if(sampleSize < 0) sampleSize = 1
		decoder.setResize(sampleSize)
		decoder.setPostProcessor { canvas ->
			canvas.drawColor(Color.RED, PorterDuff.Mode.OVERLAY)
			PixelFormat.UNKNOWN
		}

	}
}
```
#### HDR VP9 Video
To help deliver the highest quality viewing experience, Android P adds built-in support for HDR VP9 Profile 2, enabling the delivery of HDR-enabled movies. We also added support for substantially improved image compression with HEVC compressed HEIF images, which means smaller downloads and less on-device storage. And we're in the process of enhancing and refactoring the media APIs, so stay toned. 

#### Dual Camera Support for Apps
You can access streams simultaneously from devices that have logical cameras consisting of multiple physical devices, such as dual front or dual rear cameras, enabling new app possiblities. Other improvements in camera include new session parameters to reduce delays during inital capture and surface sharing, reducing the need for camera clients to stop and start camera streaming.

#### OIS and Display-based Flash
We've also added APIs for display-based flash support and access to OIS timestamps to allow apps to perform image stabilization or use this data for special effects. 

#### Indoor Positioning
Android P adds an API for the IEEE 802.11 MC Wi-Fi protocol, also known as wifi round trip time RTT, to let you take advantage of indoor positioning in your apps. Android P devices with hardware support can use RTT to measure the distance to a nearby WIFI access point that supports the protocol. The device doesn't need to connect to the AP to use RTT. And to maintain privacy, only the phone is able to determine the distance. By measuring distances to three or more access points, apps can calculate their positions with an accuracy of 1 to 2 meters.

#### GlobalPlatform Open Mobile API
Android P adds an implementation of the GlobalPlatform Open Mobile API to Android. On supported devices, apps can use OMAPI to access NFC secure elements on Android devices to enable smart card payments and other secure services. 

#### Neural Networks API 1.1 New Ops
The Neural Network API was introduced in Android 8.1 to provide support for accelerated on-device machine learning. In Android P, we're expanding and improving the API, adding support for nine new ops. On Pixel2, the DP1 build includes an accelerated driver for quantized models. 

Android P is part of our longer-term initiative to modernize the foundations of the platform and the apps that run on it. We announced recently that Google Play will require all app updates to at least target Android Oreo by November 2018 and require apps with an NDK library to provide 64-bit support in 2019. In line with these changes, Android P will warn users with a dialog when they install an app that targets an Android platform earlier than Android 4.2, and future versions will continue to increment that limit.

Every Android developer should start planning to migrate to target at least API 26 now. And we've built a checklist of resources to help and support this effort. We're always looking for ways to improve the user and developer experience. In this spirit, we're working to ensure that apps don't use non-SDK interfaces since doing so risks crashes for users and emergency rollouts for developers. 




#### Reference
[Youtube video](https://www.youtube.com/watch?v=LBBqTd6uOd4)
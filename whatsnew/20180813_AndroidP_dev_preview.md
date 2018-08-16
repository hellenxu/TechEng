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
To help deliver the highest quality viewing experience,


#### Reference
[Youtube video](https://www.youtube.com/watch?v=LBBqTd6uOd4)
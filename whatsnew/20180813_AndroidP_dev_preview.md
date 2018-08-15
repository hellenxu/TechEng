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


#### Reference
[Youtube video](https://www.youtube.com/watch?v=LBBqTd6uOd4)
### APK Analyzer: An Android Tool Time Deep Dive

#### Content
***
I love well-optimized apps, especially apps with small apk size, which makes download and update faster for users. That's why I was very excited when a new tool for analyzing apk issues was added in Android Studio. It's called the APK Analyzer and you can launch it by clicking 'Analyze APK' in the Build menu or by dragging an apk into the editor. These can be files built from your project or even apks that you don't have the sources for. When selecting an apk file, make sure you open a release build or debug build without Instant Run instrumentation, which you can obtain by going through Build->Build APK. You can check if an apk contains Instant Run instrumentation by confirming the presence of an Instant Run zip file in the archive.

Now let's go through the main screen of the analyzer. On the top here you can see some basic information about your app, such as the package name and version, along with the raw apk size and an estimated download size. This may be smaller, thanks to compression applied by the Play Store in transit. Below you can see a list of files and folders in the apk, sorted in descending order according to size. This is extremely useful when you are trying to track down low-hanging fruit for optimizing your app size, such as unoptimized drawables or uncompressed audio files. You can also see the relative percentage of total download size of each asset or folder in the rightmost column. The apk analyzer also has a detail view that's used for showing file contents. It works with several types of files. 

+ You can preview xml resources, such as layouts in their original human-readable form. This also includes the Android Manifest, so you can check out exactly what values were used when building the app.

+ You can also browse the full resource table that was compiled into the resources.arsc file. This contains a list of strings and styles for all resource configurations defined in the app.

+ And finally the analyzer contains a dex file browser letting you inspect packages, classes and method signatures. To check for any errors resulting from Proguard or MultiDex. And speaking of MultiDex, every dex file also shows a handy reference counter that tells you how close you are to the 64k limit per file.

The APK Analyzer is a great source of actionable information when trying to decide where to start optimizing your app size. And after you make your changes, you can compare the new version of the apk with a previous one using the 'Compare with...' action. So you can check how much space you gain and if there are any regressions. I strongly recommend you check out our article on reducing apk size on developer.android.com, and give the analyzer a spin.

#### References
***
[Youtube Video](https://www.youtube.com/watch?v=qR8FeCQR7L0)

[Reduce APK Size](https://developer.android.com/topic/performance/reduce-apk-size.html)

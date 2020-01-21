
### Main Content
=====
#### 0x00 General Advice

> Define scenario to improve
> Profile scenario
> Identify biggest bottleneck
> Fix bottleneck
> Verify fix by measurement
> Repeat


Preparation
=====
1. Automate your measurements
```
$ gradle-profiler --benchmark --scenario-file scenarios
```

2. Stay Up-To-Date
```
$./gradlew wrapper --gradle-version 4.9
```

3. JVM tunning
Provide enough heap space. 
Other tweaks often do more harm than good.
Spend your time on structural improvements.
```
# The only useful argument
org.gradle.jvmargs=-Xmx4g
```

Where is the Problem? [narrow down scopes after defining scenarios]
=====
1. Build Scans
Will analyse the build details
```
$ ./gradle assembleDebug --scan
```

2. Red Flags [a sign of danger] 
> Startup/Settings/buildSrc > 1s
> Single-line change ≈ clean build
> No-op build doing any work at all [no change at all]
> High GC time

2.1 Startup, buildSrc, Settings
> don't disable daemon and keep it healthy
> settings.gradle: don't scan all gradle files in your project.

```groovy
rootDir.listFiles({ File dir, String name ->
    name.endsWith(".gradle")
} as FilenameFilter)
.each {
    include "$it"
}
```

3. Configuration Time
Three parts which might contribute to configuration time:
> Applying plugins
> Evaluating build scripts
> Running afterEvaluate{} blocks




#### 0x01 




Reference
======
[Youtube Video](https://www.youtube.com/watch?v=KyNHCscbm58&list=PLdb5m83JnoaBqMWF-qqhZY_01SNEhG5Qs&index=27&t=0s)

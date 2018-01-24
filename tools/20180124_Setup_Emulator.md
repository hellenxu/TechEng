### Setting up the Android Emulator for Speed

#### Content

Configuring the Android emulator is easy with the AVD wizard in Android Studio. It offers default settings for optimal emulator performance. If you want to double-check your AVDs are configured correctly and your machine can run them with full hardware acceleration, follow these few steps.

First of all, check that your CPU supports required virtualization extensions. On Windows and Mac, only Intel VT-x is supported, and the necessary virtualization software from Intel is available through the SDK manager inside Android Studio. On some machines, VT-x is disabled by default. In that case, please refer to your motherboard or laptop menu on how to enable it. If you are using Linux, both the Intel VT-x and the AMD-v virtualization technologies are supported through KVM. In our documentation pages, linked below, we provide information on how to check if KVM is installed in your system.

Now that we have the most difficult part out of the way. Let's go through the AVD creation process.

1. On the first screen, select the device skin you like to use for development. If you don't have a high-end GPU, you might benefit from choosing a device with a lower resolution. You can always change the size later by editing the AVD. 

2. Next, choose the system image. There are three important guidelines for this step. Choose one of the more recent releases with a higher API level number as those contain the newest fixes and optimizations. Make sure you are selecting an x86 image, and also one marked as having Google APIs, which means you will be able to test apps that make use of Google Play Services or integrated with some core apps, such as Google Maps.

3. On the last step, you can select from different graphics acceleration modes. But leaving it on Automatic should detect your CPU capabilities and select the best mode to run. If you are having problems, it's best to always make sure your graphics drivers are up to date.

You can read more about the various GPU settings in our documentation, also linked in the description. If you encounter any bugs when running the emulator, please read the Android Studio bug finding process and report them using the Issue Tracker.

#### References
[Youtube Video](https://www.youtube.com/watch?v=lj_CTXVrUto&t=2s)
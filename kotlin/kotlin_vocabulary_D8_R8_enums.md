### Kotlin Vocabulary: D8, R8, and enums

When you're learning or using a new language, it's important to understand what the features are that the language offers, and whether there's any overhead associated with those features. This gets particularly interesting in Kotlin because it offers features that are not in the Java programming language, even though it compiles down to the byte code of that other language. So how does that work? And is there overhead associated with any of those features? And if there is, is there anything that we can do about that? I'm going to talk about enums and switch statements and some of the non-obvious overhead associated with those, and about how you can use the Android R8 compiler to optimize your app and reduce that overhead.

#### D8 and R8
First, I want to talk about D8 and R8. In fact, I want to talk about three compilers. There's actually three compilers that kick in for the Kotlin code that you write for your Android applications. 

First of all, the Kotlin compiler runs and converts your Kotlin code into byte code for the Java programming language. It just translates it along the way, which is great, except we don't run that byte code on Android devices. Instead, we run something called DEX, which stands for Dalvik Executable. Dalvik was the name of the original runtime on Android. Now, the runtime is called ART for Android Runtime.

D8 comes in, the second compiler, and it translates from Java programming language byte code to DEX code. And at that point, you can run your program. Or you can optionally use the third compiler called R8, which is used for optimizing and shrinking your application -- basically a replacement for ProGuard. So since it's not enabled by default, if you want to use this, which you may for some of the optimizations we're going to talk about, you need to enable it. If you go to your build.gradle file for your application, then you can set `minifyEnabled` to true, and that will force R8 to run at the end of all the other compilation to make sure you get your shrunk and optimized app.

```groovy
android {
    buildTypes{
        release {
            minifyEnabled true

            proguardFiles getDefaultProguardFile(`proguard-android-optimize.txt`), `proguard-rules.pro`
        }
    }
}
```

#### Enums

I'm going to talk about a feature and overhead that is essentially true whether you're writing in Java code or Kotlin code. But it's interesting what we can do about some of that overhead once we introduce R8 into the mix. Enums by themselves don't have any overhead that is non-obvious. If you're using an enum in Kotlin, then that's going to translate to an enum in the Java programming language -- same kind of thing.The trick comes if you use switch statements. So first of all, let's take a look at some enum code.

```kotlin
    enum class BlendMode {
        OPAQUE,
        TRANSPARENT,
        FADE,
        ADD
    }
```

So we've declared this enum code for this `BlendMode`. We have four values there. For each one of those, we're going to enter the switch statement or a when statement in Kotlin. And based on the value of the enum, we're going to execute some code. We're going to call another function. Pretty straightforward.

```kotlin
fun blend(b: BlendMode) {
    when (b) {
        BlendMode.OPAQUE -> src()
        BlendMode.TRANSPARENT -> srcOver()
        BlendMode.FADE -> srcOver()
        BlendMode.ADD -> add()
    } 
}
```

Now, if you take a look at what it is actually compiled down to in the JVM byte code, it looks something like this.

```java
public static void blend(@NotNull BlendMode b) {
    switch(BlendingKt$WhenMappings.$EnumSwitchMapping$0[b.ordinal()]) {
        case 1: {
            src();
            break;
        }
        // ...
    }
}
```
So instead of just doing a switch on the value, it actually calls into this array `BlendingKt$WhenMappings.$EnumSwitchMapping$0`. Where did that array come from? And the array is stashed in this generated class file. Where did the class file come from? So what is going on here?

It turns out that for binary compatibility reasons, the code can't simply switch on the value, the ordinal value of enums, because that could break. So you could have a library that has an enum, and if you change the order of those items in the enum, then you could break somebody's application, even though like actually it looked the same in the code; they were just in different order. But it was the implementation detail of the ordering that caused the break. So instead, they take the ordinal value and map it to another value. And then, no matter what you do with these enums, code that is built with that library will still run. So all of that's great. But it does mean that something is going to get generated on your behalf. So if we look at the generated code, it looks something like this. So there's the class that gets generated. And inside of that is an array where we're going to do the mapping and then some static code, which actually performs the mapping operation.

```java
public final class BlendingKt$WhenMappings {
    public static final int[] $EnumSwitchMapping$0 = new int[BlendMode.values().length];

    static {
        $EnumSwitchMapping$0[BlendMode.OPAQUE.ordinal()] = 1;
        $EnumSwitchMapping$0[BlendMode.TRANSPARENT.ordinal()] = 2;
        $EnumSwitchMapping$0[BlendMode.FADE.ordinal()] = 3;
        $EnumSwitchMapping$0[BlendMode.ADD.ordinal()] = 4;
    }
}
```

So interestingly, we have one single when statement in there, or the switch statement that we saw earlier. If we had another when statement somewhere in the code, we would generate another array and another array. So all of these aren't that big deal. But it does mean that unbeknownst to you, a class is generated on your behalf and then some number of arrays inside of that class, just taking more time. Adds class loading time and instantiating all these things and loading up when your application is ready to go.

So is there something we could do about that? Well, it turns out there is. If we use R8, it's actually smart enough to do the right thing here. 
```java
public static void blend(@NotNull BlendMode b) {
    switch(b.ordinal()) {
        case 0: {
            src();
            break;
        }
        // ...
    }
}
```

So R8 is an interesting optimizer. It can see everything in your application. It sees all the code, whether it's the code that you wrote, or the library code that you're building against. And it can make decisions about optimizations to know that, for example, in this situation, it can avoid that overhead. It does not need that mapping information because it knows that you are only going to use these enums in a certain way. And so, it can actually just call into the ordinal value instead, and it avoids having to create the class, avoids having to create the array, and just creates the optimal code that you want to begin with. 

So check out R8, check out Kotlin, and enjoy writing better Android applications with Kotlin.
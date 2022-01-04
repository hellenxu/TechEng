### Gradle and AGP Build APIs: How to write a plugin

#### Context
Starting version 7.0, android gradle plugin now offers stable extension points manipulating variant configurations, and the produced build artifacts. Some parts of the APIs were finalized only recently, so I'm going to use the 7.1 version of AGP throughout this video. Let's dive in to see what you can achieve with custom plugins compared to the regular DSL configuration. 

#### Write your own custom plugin
I'll start with the new clean project. If you want to follow along, you can create a new project by selecting the `Basic Activity` template. Let's start with creating a task and printing out `hello world!`. To do this, in the app level `build.gradle` file, I'll register a new task, and name this take `hello`. Now, the task is ready, let's print out Hello, and also add the project name. Notice this gradle file belongs to the app module, so project name will return this module's name, which is App. Instead, I'll use the project parent name, which returns the name of the project. 

```kts
tasks.register("hello") {
    println("Hello from " + project.parent?.name)
}
```

Now it's time to run the task. Looking at the task list, I can see that my new task is listed here. I double click the `hello` task, or execute this task through terminal, and see the hello message printed in the build output. When I checked the logs, I can see that this message is printed during the configuration phase. Configuration phase is really not about executing the task function, like printing `hello` in this example. Configuration phase is a time to configure a task to influence its execution. You can tell the task about inputs, parameters, and where to put the outputs. Configuration phase runs regardless of which task is requested to run. Running time-consuming code in the configuration phase can result in long configuration times. The task execution should only happen during the execution phase, so we need to move this print call to the execution phase. To do that, I can add doFirst or doLast, which will print the hello message at beginning or at the end of the execution phase respectively. When I run the task again, this time I can see that the hello message is printed during the execution phase. 

```kts
tasks.register("hello") {
    doLast {
        println("Hello from " + project.parent?.name)
    }
}
```

Right now, my custom task is located in the `build.gradle` file. Adding custom tasks to the `build.gradle` file is a simple way to create custom build scripts. However, as my plugin gets more complicated, this doesn't scale well. We recommend placing the custom task and plugin implementations in a build source folder. 

Before writing any more codes, let's move my task to build source. I'll create a new folder and name it buildSrc. Next, I create a `build.gradle` file for the plugin project, so gradle automatically adds this project to build. This is a top-level directory in the root project folder. Notice I don't need to add this as a module in my project as Gradle automatically compiles the code in this directory and puts it in the class part of your build script. Next, I create a new source folder and a class name `HelloTask`. I convert this new class to an abstract class and extend default task. Next, I'll add a new function called `taskAction` action, annotate this function with `@TaskAction` annotation, and move my custom task code from `build.gradle` to this function. Now that my task is ready, I'll create a new plugin class, which needs to implement plugin, and override the `apply` function. Gradle will call this function and pass in the project object. To register the `HelloTask`, I'll call register on `project.tasks`, and give this new task a name. At this point, I can also declare that my task depends on another task.  

```kotlin
abstract class HelloTask: DefaultTask() {
    @TaskAction
    fun taskAction() {
        println("Hello from ${project.parent?.name}")
    }
}

class CustomPlugin: Plugin<Project> {
    override fun apply(project: Project) {
        project.tasks.register<HelloTask>("hello") {
            dependsOn("build")
        }   
    }
}
```

Next, let's apply the new plugin. Note that if my project has more than one module, I could re-use this plugin by adding it to other `build.gradle` files as well. Now I'll execute the HelloTask, and observe that my plugin works just like before. Now that I moved my task to build source, let's take it a step further and discover the new Android Gradle Plugin APIs.

```kts
\\build.gradle
apply<CustomPlugin>()

```

AGP offers extension points on its lifecyle while building the artifacts. To start with variant API, let's first discuss what a variant is. Variants are different versions of your app that you can build. Let's say, besides a fully-functioning app, you also want to build a demo version of your app or an internal version for debugging purposes. You can also target different API levels or device types. Variants are created by combining build types, such as debug and release, and product flavors that are defined in the build script.

Going back to my plugin, let's say I want to create a new build type for staging with these properties. This creates a new build type based on the debug build type. It's a hostname and the suffix on the applicationId. Using the declarative DSL in your build file to add a build type is perfectly fine. However, doing that in the code gives your plugin a way to influence the build in ways that are not possible or difficult to express using declarative syntax. AGP starts with the build by parsing the build script and the properties set in the Android block. The new variant API callbacks allow me to add a finalized DSL callback from the AndroidComponentExtension. In this callback, I can change the DSL objects before they are used in variant creation. I'll create a new build type and set its properties. Notice in this phase, I can create or register new build types and set their properties. At the end of this phase, AGP will lock the DSL object so they cannot be changed. If I run the build again, I can see that a staging version of the app is built.

```kts
create("staging") {
    initWith(getByName("debug"))
    manifestPlaceholders["hostName"] = "internal.example.com"
    applicationIdSuffix = ".debugStaging"
}
```

```kotlin
class CustomPlugin: Plugin<Project> {
    override fun apply(project: Project) {
        project.tasks.register<HelloTask>("hello") {
            dependsOn("build")
        }   
    }

    val extension = project.extensions.getByName("androidComponents") as ApplicationAndroidComponentsExtension

    extension.finalizeDsl { ext ->
        ext.buildTypes.create("staging").let { buildType ->
            buildType.initWith(ext.buildTypes.getByName("debug"))
            buildType.manifestPlaceholders["hostName"] = "internal.example.com"
            buildType.applicationIdSuffix = ".debugStaging"
        }
    }
} 
```

Now, let's say one of my tests is failing, and I want to disable unit tests to build an internal version to figure what's wrong. To disable unit tests, I can use the beforeVariants callback, which allows me to make such changes through the `variantBuilder` object. Here I'll check if the current variant is the one I created for staging. Next, I'll disable the unit tests and set a different minSDK version. Note that if I want to change the debuggable property, I get an error that this is a read-only property when beforeVariants is called. However, I can go back to `finializeDsl` callback, and set this property before the DSL is finalized. After this phase, the list of components and artifacts that will be created are now finalized. If you want to see more samples like this, make sure to checkout the gradle recipes repo, which I linked in the notes below. 

```kotlin
class CustomPlugin: Plugin<Project> {
    override fun apply(project: Project) {
        project.tasks.register<HelloTask>("hello") {
            dependsOn("build")
        }   
    }

    val extension = project.extensions.getByName("androidComponents") as ApplicationAndroidComponentsExtension

    // ...
    extension.beforeVariants { variantBuilder ->
        if (variantBuilder.name == "staging") {
            variantBuilder.enableUnitTest = false
            variantBuilder.minSdk = 23
            variant
        }    
    }
} 
```
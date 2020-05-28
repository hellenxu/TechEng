### Kotlin Vocabulary: Sealed Classes

#### Context

We need to limit the options of possibilities: 
1,) a request either succeeds or fails;
2,) a user can only be a pro user or a standard user.

To module this, we could use an enum, but this carries a number of limitations. Enum classes only allow a single instance of each value and can't encode more information on each type. 

For example, for the error case, we can't have an associated exception. 

```kotlin
enum class Result {
	Success, 
	Error(val e: Exception) // compile error
}

``` 

You could use an abstract class and a number of extensions, but this loses the restrict set of types advantage brought by enum. Sealed classes provide the best of both worlds: 
1,) the freedom of representation of abstract classes;
2,) the restrict set of types of enums.

In this video, I will tell you more about how to use sealed classes, about the autocomplete power brought by the IDE, and we'll end by looking under the hood at how sealed classes are implemented in decompiled java code.


#### Get hands dirty

Like abstract classes, sealed classes allow you to represent hierarchies. The child classes can be any type of classes, a data class, an object, a regular class, or even another sealed class. But unlike abstract classes, you have to define these hierarchies in the same file, or as nested classes. Trying to extend a sealed class outside the file it was defined yields a compile error. Often, we want to handle all possible types. But what if we need to add a new type of result, like in progress. Rather than relying on our bad memory and an ID search to ensure that all usages handle the new class, the compiler can give us an error if a branch is uncovered. `when`, like the if statement, only requires us to cover all options to be exhaustive by producing a compiler error when it's used as an expression. To get this code benefit even when we're using `when` as a statement at the extension helper property.

```kotlin
val <T> T.exhaustive: T
	get() = this

```

By adding .exhaustive, if a branch is missing, the complier will give us the same error we saw previously. As all subtypes of a sealed class are known, the IDE can fill all possible branches of a `when` statement for us. But this autocomplete feature really shines when we deal with more complex sealed classes hierarchies as the IDE can recognize all branches. 

Let's say we have two types of error: recoverable error and non-recoverable error. So now our result has more possible options. The IDE will generate all of these branches.

```kotlin
sealed class Result<out T: Any> {

	data class Success<out T: Any>(val data: T) : Result<T>()

	sealed class Error(val exception: Exception) : Result<Nothing>() {
		class RecoverableError(exception: Exception): Error(exception)
		class NonRecoverableError(exception: Exception): Error(exception)
	}

	object InProgress: Result<Nothing>()
}

```

This is the type of functionality that can't be implemented with abstract classes as the compiler doesn't know the inheritant hierarchies, therefore, the IDE can't generate the branches. 


#### Under the hood
What makes sealed classes behave as they do? Let's take a simple example, and see what's going on in the decompiled java code. First, the MetaData of a sealed class keeps the list of child classes, allowing the compiler to use this information where needed. The result is implemented as an abstract class with two constructors: a private default constructor and a synthetic constructor that can only be used by the Kotlin compiler. So this means that no other classes can directly call the two constructors. If you look at the decompiled code of the Success class, we see that it calls through the synthetic constructor.

```java


```

That's all I have to say about sealed classes. Start using them to module restricted class hierarchies, allowing the compiler and the IDE to help people avoid the type errors.

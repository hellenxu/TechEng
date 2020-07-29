### Kotlin Vocabulary: Object

#### Context
In Java, the `static` keyword is used to denote methods and properties that belong to an object, not an instance of an object. The `static` keyword is also used to create singletons, one of the most popular design patterns. Singleton pattern helps you to create a single instance of an object, which can be accessed and shared by other objects. Kotlin has a more elegant way to deal with this. You can use a single keyword, `object`, to implement the singleton pattern. 

#### Differences of Implementing Singleton in Java vs Kotlin
What do we need singleton?
Singleton is a design pattern, which ensures that the class has only one instance, and provides a global point of access to that instance. The singleton pattern is particularly usable for objects which need to be shared between different parts in your app, and for resources that are expensive to create. To guarantee that the class has only one instance, you need to control how the object is created. To create a class with only one instance, make the constructor private, and create a publicly accessible static reference of the object. While doing this, you don't really want to create the singleton at startup, since singletons are used for objects, which are expensive to create. To initialize easily, provide a static method, which checks if the object is created. This method must return to previously created instance or call the constructor and return a new instance. This code seems fine, but there is a major issue: this code is not thread safe. At anytime, one thread can pass the if check, but be put on hold while another check creates the singleton. Then the first thread resumes inside if block, it create another instance. To fix the threading issue, you can use double-check locking.


```java
// thread unsafe singleton implementation
public class Singleton {
	private Singleton(){}
	private int count = 0;

	private static Singleton INSTANCE;

	public static Singleton getInstance() {
		if (INSTANCE == null) {
			INSTANCE = new Singleton();	
		}
		return INSTANCE;
	}

	public int count() {
		return count++;
	}
}

```

With double-check locking, if the instance is null, `synchronized` keyword creates a lock, and a second check ensures the instance is still null, only after that, creates the singleton instance. Yet, this is not enough. And the instance also needs to be marked as `volatile`. `Volatile` keyword tells the compiler that this variable might be modified asynchronously by concurrent threads. All of this leads to a lot of boilerplate, which you need to repeat each time you implement a singleton. Since this code is too complicated for such a simple task, enums are used for creating singletons in Java most of the time. 

```java
// double-check locking singleton implementation
public class Singleton {
	private Singleton(){}
	private int count = 0;

	private volatile static Singleton INSTANCE;

	public static Singleton getInstance() {
		if (INSTANCE == null) {
			synchronized(Singleton.class) {
				if (INSTANCE == null) {
					INSTANCE = new Singleton();	
				}
			}
		}
		return INSTANCE;
	}

	public int count() {
		return count++;
	}
}
```

Now let's take a look at Kotlin. Kotlin doesn't have static methods or fields. So how can we implement this functionality? Actually, Android Studio or IntelliJ can help us to figure out. When you convert the singleton code in Java to Kotlin, all static properties and methods are moved into a companion object. The converted code works as expected.

```kotlin
// converted singleton implementation
class Singleton private constructor() {
	private var count = 0

	fun count(): Int {
		return count++
	}

	companion object {
		private var INSTANCE: Singleton? = null
		val instance: Singleton?
			get() {
				if (INSTANCE == null) {
					synchronized(Singleton::class.java) {
						if (INSTANCE == null) {
							INSTANCE = Singleton();	
						}
					}
				}
				return INSTANCE
			}
	}
}


```
But we can make it simpler by removing the constructor and companion keyword from object. When you want to use `count()` method you can access it over the singleton object.

```kotlin
object Singleton {
	private var count = 0

	fun count(): Int {
		return count++
	}
}

class Caller {
	fun doCall() {
		Singleton.count()
	}
}

```

#### How to Use `object` and `companion object`, and What's under the Hood

In Kotlin, object is a special class that only has one instance. If you create a class with the `object` keyword instead of `class`, the copying compiler makes the constructor private, creates a static reference for the object, and initializes the reference in a static block. Static blocks are called only once when the static field is first accessed. The JVM handles the static block in a similar way how it handles synchronized blocks. When the singleton class is initialized, the JVM acquires a lock on the synchronized block, making it impossible for another thread to access it. When the lock is released, the singleton instance is already created, so the static block won't execute again. This guarantees that there is only one instance of the singleton, which fulfills the singleton contract. Plus, the object is both thread safe and easily created the first time it's accessed. 

Companion object is similar to object. Companion objects are always declared in a class. And their properties can be accessed by using the host object. The companion object doesn't require a name. If the companion object has a name, the caller can access the members using the companion object's name. Alternatively, any caller can access the `count` method.
```kotlin
class AnotherClass {
	companion object Counter {
		private var count: Int = 0

		fun count(): Int {
			count++
		}
	}
}

```

 The companion object decompiles into an inner class with a private constructor. The host class initializes the inner class through a synthetic construnctor which only it can access. The host class keeps a public reference to the companion object, which is accessible from other classes. If the companion object is declared without a name, the Kotlin compiler uses `Companion` as the name of the static class. 
```java
public final class AnotherClass {
	private static int count;
	public static final AnotherClass.Counter Counter = new AnotherClass.Counter((DefaultConstructorMarker)null);

	public static final class Counter {
		public final int count () {
			return AnotherClass.count = AnotherClass.count + 1;
		}

		private Counter() {}

		// SFF: synthetic method
		public Counter(DefaultConstructorMarker $constructor_marker) {
			this();
		}
	}
}

```

So far, we've seen the object keyword used in object declarations. Object keyword can also be used in object expressions. When used as an expression, object keyword helps you to create anonymous objects and anonymous inner classes. Let's say you need a temporary object to hold some values. You can declare and initialize your object with the desired values on the spot and access them later. 
```kotlin
val tempValues = object: {
	var value = 2
	var anotherValue = 3
	var someOtherValue = 4
}

tempValues.value += tempValues.anotherValue
```
In the generated code, this translates into anonymous Java class marked by undefined type to store the anonymous object with generated getters and setters. The object keyword also helps you to create anonymous classes without writing any boilerplate code. The object keyword helps you to create thread safe singletons, anonymous objects, and anonymous classes with less code. With the object and companion object, Kotlin generates all the code, which you would achieve by static keyword in Java. Plus, you can use an object expressions to create anonymous objects and classes without any boilerplate code.
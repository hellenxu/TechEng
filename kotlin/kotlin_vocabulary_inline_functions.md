### Kotlin Vocabulary: Inline Functions

#### Context
You know all those util files you created with all sorts of small functions that you end up using a lot throughout your app? If your utility functions get other functions as parameters, chances are you can improve the performance of your app by saving some extra object allocations with one keyword, inline.

Let's see what happens when you pass these short function around, what inline does under the hood, and what you should be aware of working with inline functions.

#### Under the Hood
Let's take an example. Let's say that you use `SharedPreferences` a lot in your app, so you created this utility function to reduce the boiler-plate. Then you use it to save a string token. 

```kotlin
fun SharedPreferences.edit(
	commit: Boolean = false,
	action: SharedPreferences.Editor.() -> Unit
) {
	val editor = edit()
	action(editor)
	if (commit) {
		editor.commit()
	} else {
		editor.apply()
	}
}

private const val KEY_TOKEN = "token"

class PreferencesManager(private val preferences: SharedPreferences) {

	fun saveToken(token: String) {
		preferences.edit {
			putString(KEY_TOKEN, token)
		}
	}
}
```
Now, let's see what's going on under the hood when `preferences.edit{}` is called. If you look at the Kotlin bytecode, we see there's a new called. So a new object has been created even in our code, we actually didn't call any object constructor. Let's check decompiled code to make this a bit friendly. The `edit` SharedPreferences extension function we defined is as follows:

```java
public final class PreferencesMananger {
	private final SharedPreferences preferences;

	public final void saveToken(@NotNull final String token) {
		Intrinsics.checkParameterIsNotNull(token, "token");
		PreferenceManagerKt.edit$default(this.preferences, false, (Function1)(new Function1() {
			public Object invoke(Object var1) {
				this.invoke((Editor)var1);
				return Unit.INSTANCE;
			}

			public final void invoke(@NotNull Editor $this$edit) {
				Intrinsics.checkParameterIsNotNull($this$edit, "$receiver");
				$this$edit.putString("token", token);
			}
		}), 1, (Object)null);
	}
}

```

Remember each higher order function we created leads to a Function object creation and memory allocation that introduces runtime overhead. To improve the performance of our app, we can avoid the Function object creation by using the `inline` keyword. Now the called in bytecode doesn't contain any new calls.

```java
public final class PreferencesManager {
	private final SharedPreferences preferences;

	public final void saveToken(@NotNull String token) {
		Intrinsics.checkParameterIsNotNull(token, "token");
		SharedPreferences $this$edit$iv = this.preferences;
		boolean commit$iv = false;
		int $i$f$edit = false;
		Editor editor$iv = $this$edit$iv.edit();
		Intrinsics.checkExpressionValueIsNotNull(editor$iv, "editor");
		int var7 = false;
		editor$iv.putString("token", token);
		editor$iv.apply();
	}

	public PreferencesManager(@NotNull SharedPreferences preferences) {
		Intrinsics.checkParameterIsNotNull(preferences, "preferences");
		super();
		this.preferences = preferences;
	}
}
```

So what happened? Because of the `inline` keyword, the compiler copies the content of the inline function to the call site avoiding creating a new Function object. If you try to mark as inline a function that doesn't accept another function as a parameter, then you wouldn't get significant performance benefits. And actually the IDE would even tell you that, suggesting you to remove it.

Because inlining may cause the generated code to grow a lot, make sure that you avoid inlining the large functions. So for example, if you check the Kotlin standard library in files like `Collections.kt`, you will see that most of the inline function has only one to three lines. When using inline function, you're not allowed to keep the reference to the functions passed as a parameter or pass to a different function. You will get a compiler error, saying `illegal usage of inline parameter.` So for example, let's modify the edit method and add another parameter and `importantAction` let's call it, and then passed to a different function. We can see that `myFunction(importantAction)` produce an error. 

We have two cases here: 
1,) If you have multiple functions as parameters, and you only need to keep a reference to one of them, then you can mark it as no inline. By using `noinline`, the compiler will create a new Function object only for that specific function, but the rest will be inlined. If we check the bytecode, we see that a new call indeed appeared. In the decompiled code, we can see that everything is inline except for that `importantAction` function, and only here a new Function object is created.

```kotlin
inline fun SharedPreferences.edit(
	commit: Boolean = false,
	noinline importantAction: Int.() -> Unit = { },
	action: SharedPreferences.Editor.() -> Unit
) {
	//...
}
```

2,) if your function only has one function as parameter and you need to store with that reference. So here just prefer not using inline at all. If you do add inline, then you have to mark your parameter with `noinline`, but this could cause low performance benefits by inlining the method. So there's no point in using it. 


#### Summary
So to summarize, to decrease the memory allocations caused by lambda expressions, use the `inline` keyword. Make sure you apply to small functions that call lambda as a parameter. If you need to keep a reference to a lambda or pass it as an argument to another function, use that `noinline` keyword. So start inlining to start saving with Kotlin.

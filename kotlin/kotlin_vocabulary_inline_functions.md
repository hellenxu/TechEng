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


```
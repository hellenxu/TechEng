### Architecture Components: ViewModel

#### Content
***
The Architecture Components Lifecycle Library is designed to help you solve common Android Lifecycle problems and to make your app more maintainable and testable. This is an introduction to ViewModel. 

A ViewModel holds your app UI data in a lifecycle-conscious way that also survives configuration changes. Now, here is a real world example of why that might actually be useful. Rotating your phone is considered a configuration change. Configuration changes cause your whole activity to get tore down and then recreated. If you don't properly save and restore data from the destroyed activity, you may lose data and end up with weird bugs or even crashes, like this. That's no good. So enter the ViewModel which survives configuration changes. Instead of storing all of your UI data in the activity, put it in the ViewModel instead. Now, this does help with configuration changes, but that's also a good software design. One common pitfall with developing for Android is to put a lot of variables, logic, data and other things in your activities or fragments. And what this does is it creates a large unmaintainable mess of class and violates the Single Responsibility Principle. You can use ViewModels to easily define up that responsibility. ViewModels will hold all of the UI data for an Activity, then the Activity is only responsible for knowing how to draw that data on the screen and for receiving user interactions. But it doesn't actually have to process them. If your app loads and stores a lot of data, I also suggest making a repository class, as described in the "Guide to Android Architecture."

Now, you should make sure your ViewModels don't become bloated like your Activities and Fragments were. To avoid this, you can create presenter classes or you can implement a more fully fledged, clean architecture. There is tons of documentation linked. Okay, so let's actually make a ViewModel.

To make a ViewModel, you extend the ViewModel class, and put all of the UI related instance variables that were previously in your activity into your ViewModel. You should encapsulate these variables by providing getter methods.

```java
public class MyViewModel extends ViewModel {
	private User mCurrentUser;

	public User getCurrentUser () {
		return mCurrentUser;
	}
}
```

Okay, now in your activity's onCreate, you get the ViewModel from a Framework utility class called ViewModelProviders. Notice the ViewModelProvider takes an activity instance. This is the mechanism that allows you to rotate the phone, get a technically new activity instance, but insure that that instance is always associated with the same ViewModel. With the ViewModel instance, you can use the getters to access the UI data from your activity. Documentation is linked. 

```java
public class MyActivity extends AppCompatActivity {

	public void onCreate(Bundle savedInstanceState) {
		MyViewModel model = 
		ViewModelProviders.of(this).get(MyViewModel.class);
		User user = model.getCurrentUser();
	}
}
```

Now, you may want to modify the default constructor of your ViewModel, which currently takes no parameters. 

1. You can get around this by using the default constructor and immediately calling a set method with the parameter that you want to pass in. 
2. Alternatively, you can use the ViewModelFactory class to create a custom constructor.

Documentation is linked.

Now, as a warning, you should never pass context into ViewModels. So this means no passing fragments, activities or views. ViewModels don't care about specific lifecycle of a single activity or fragment, which can be destroyed or recreated many times during the lifecycle of that ViewModel. So let's say you accidentally do pass an activity to the ViewModel. When you rotate the screen and the activity is destroyed, you now have a ViewModel that's holding a reference to a useless destroyed activity, and this is memory leak. Now, if on the other hand, you find yourself needing an application context, you can use the AndroidViewModel subclass. This includes a reference to an application for you to use.

Okay, one more warning. ViewModels are not a replacement for onSaveInstanceState(). Now, as I said, ViewModels survive configuration changes, but it doesn't mean they survive a process related shutdown due to resource restrictions. So make sure to use your ViewModels to store as much UI data as possible so that you don't need to reload or regenerate that data during a configuration change. onSaveInstanceState() on the other hand, is meant to store the smallest amount of data that you need to actually restore UI state if your process is shut down by the framework. So for example, you might use onSaveInstanceState() to store the database id for an object, instead of storing the full object. Documentation is linked.

Okay, I am going to finish off with two more ViewModel facts. You can use your models to share data between different fragments in an activity, and this removes the need to have the fragments reference each other or to have them communicate through the parent activity. Documentation is linked. Now ViewModels can also be used to create reactive UIs using another architecture component class called LiveData. LiveData can be stored inside of ViewModels and then can automatically notify linked fragments or activities when it's updated. Learn more about LiveData, watch the introduction.

```java
// ViewModels + LiveData = Reactive UI
public class MyViewModel extends ViewModel {
	private MutableLiveData<List<User>> users;

	public LiveData<List<User>> getUsers() {
		if(users == null) {
			users = new MutableLiveData<List<Users>> ();
			loadUsers();
		}
		return users;
	}

	private void loadUsers() {
		// do async operation to fetch users.
	}
}
```

So hopefully, this inspires you to try out the new ViewModel class in your own apps. To learn more about ViewModels or any of the information I mentioned, check out the documentation, which, of course, is linked below.

#### Reference
***
[Youtube Video](https://www.youtube.com/watch?v=c9-057jC1ZA)

[Guide to App Architecture](https://goo.gl/Vqtt34)

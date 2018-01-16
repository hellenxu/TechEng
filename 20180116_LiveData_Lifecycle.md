### Architecture Components: LiveData and Lifecycle

#### Content
***
The architecture components - Lifecycle library is designed to help you solve common Android lifecycle headaches and to make you app more maintainable and testable. This here is an introduction to the LiveData and Lifecycle classes.

LiveData is a data holder class that is also lifecycle-aware. It keeps a value and allows the value to be observed. Now, a primary use-case of LiveData is to update the UI. You have a model object, such as this user object, which is wrapped by LiveData. Then you have a UI component, like an Activity that will observe a LiveData. When details of the LiveData change, the activity is notified and updates its UI. LiveData makes it easy to keep what's shown on screen in sync with the data. Let's take a look at the code.

```java
public class NameViewModel extends ViewModel {
	//Create a LiveData with a String
	private MutableLiveData<String> mCurrentName;

	public LiveData<String> getCurrentName() {
		if(mCurrentName == null) {
			mCurrentName = new MutableLiveData<String>();
		}
		return mCurrentName;
	}

	//Rest of the ViewModel...	
}
```

LiveData objects will usually be kept in the ViewModel class. If you are not sure what the ViewModel is, you should check out this video(Architecture Components: ViewModel). You will always define the type of object the LiveData holds here, and it could be any object type. Next, in the onCreate of the activity, you make an observer object. The onChanged method defines what happens when the LiveData object actually changes. In this example, the method updates a TextView in the activity. Then all that's left is to have the observer subscribes to the LiveData to receive updates. If you observe a LiveData object that already contains a value, your observer immediately gets notify with that value. Once observation starts, when the LiveData updated, the UI will automatically update.

```java
public class NameActivity extends AppCompatActivity {
	private NameViewModel mModel;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		mModel = ViewModelProviders.of(this).get(NameViewModel.class);

		final Observer<String> nameObserver = 
			new Observer<String> () {
				@Override
				public void onChanged(String newName) {
					mNameTextView.setText(newName);
				}
			};
		mModel.getCurrentName().observe(this, nameObserver);
	}
}
```

What makes LiveData different from other observables is that it is also lifecyle-aware. Now, to understand what this means though, you first need to know about the Lifecycle class. A Lifecycle is an object that defines an Android Lifecycle. Relatedly, LifecycleOwner is an interface for objects that have a lifecycle, and a LifecycleObserver is an interface for observing a LifecycleOwner. As of Support Library version 26.1, both Activity and Fragment implement LifecycleOwner. These classes provide ways for you to both query the state of lifecycle and to receive callbacks for lifecycle events. LiveData leverages this. When a LiveData observer is created, it is associated with a LifecycleOwner. Observer objects are that only notified of their updates if their associated Lifecycle is in the started or resumed state. Observers also automatically remove themselves when their LifecycleOwner is destroyed. LiveData is therefore protected against accidentally updating a UI component that's in the background or destroyed without you having to do anything extra.

So how is LiveData actually updated? setValue() and postValue() are two methods for updating LiveData. Use setValue if you're running on the UI Thread, and use postValue if you're running on a background thread. Additionally, Room and LiveData go together like peanut butter and jelly. Room can return LiveData objects which are automatically notified when the database changes and have their data loaded in a background thread for you. This makes it very easy to have the UI update when your database updates. You can learn more about Room in the introduction to Room video. 

While we're on the topic of updating LiveData, let's talk about a cancellation. You'll usually work with LiveData and MutableLiveData. Now, as the name implies, MutableLiveData is mutable. It exposes setValue and postValue methods. LiveData, on the other hand, is read-only. In general, your ViewModel should be the only class directly updating LiveData. This means within the ViewModel class, you can use MutableLiveData, and then from the outside of the ViewModel, you can expose played LiveData objects. As your LiveData expertise increases, you might find that you need to manipulate your LiveData. You can use built-in transformations or you can make your own.

The built-in map transformation lets you apply a function to the output of one LiveData, and then propagate the results down stream to another LiveData. In this example, whenever the user LiveData changes, username is notified, runs the map function and then emits the resulting full name.

```java
LiveData<User> userLiveData = ...;
LiveData<String> userName = 
	Transformations.map(
		userLiveData, user -> {
			user.name + " " + user.lastname
		}
	);
```

The switchMap() transformation is a lot like map(), but instead of the mapping function emmitting a value, it emits an entirely new LiveData. An example use case is if you have a bunch of users, perhaps stored in a Room database, you can use the switchMap() to change which user you're actually observing. You input the ID here and then you need to provide a function that returns a user LiveData given that ID. That function could then emit a totally new LiveData, but you wouldn't need to update the observers. Here is the code with a LiveData for updating the ID, a function that takes that ID and outputs the related user LiveData and the switchMap function.

```java
class MyViewModel extends ViewModel {
	
	private MutableLiveData<String> userId = ...;
	
	private void setUserId(String id) {
		userId.setValue(id);
	}

	private LiveData<User> getUser(String id) {
		...
	}

	public final LiveData<User> user = 
	Transformations.switchMap(
		userId, id -> getUser(id)
	);
}
```

If you want to make your own custom data transformation, you should take a look at MediatorLiveData. MediatorLiveData includes methods to add and remove source LiveData objects. You can then combine and propagate events from all the sources down stream. One use case is if you want to display a LiveData object that could be updated either from the local database or the network. You can take two sources and add them to the MediatorLiveData. The first source would be a local database LiveData object, and the second source would be a network LiveData object. Then your activity would only need to observe the single MediatorLiveData object, but it would receive unchanged updates for both of these sources. Getting started with LiveData is simple, but there is a lot of room for potential experimentation with this Lifecycle-aware observable. Hopefully this has inspired you to build something. And as always, the documentation is linked.


#### References
***
[Youtube Video](https://www.youtube.com/watch?v=jCw5ib0r9wg&t=3s)

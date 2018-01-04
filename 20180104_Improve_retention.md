### Improving Retention for Android Apps

#### Content
***
If you're like many, you get a new phone every 2 years. With some napkin math,
you will see this means about half of your users will get a new phone this
year. What happens when a user gets a new phone? Google Play will re-install
your apps and if you do nothing, you will create a cold welcome. You will show
a blank login form, like a total stranger who has never created an account. No
one enjoys that, and there are a few ways you can get a warm welcome.

You can simplify login by using Google Sign In, Smart Lock and Autofill, and
you can easily move all your app settings to your new phone by using Auto
Backup.

You're probably familiar with Google Sign In. Instead of needing a user name
and password, users press a single button to securely log them into your app
with their gmail account or any email address, and if you enable it they can
even skip the button. By adapting Google Sign In, you can lift registration
and activation as well as retention in your IOS, web and android applications.

For users with a password, create a warm welcome with Smart Lock for
passwords. Smart Lock is a password manager and it offers to save users'
passwords when they log in. If they decline, it wouldn't bug them again. But
if they accept, it securely stores the password in the cloud. Your Android and
web applications can then fetch the password programmatically. So you don't
have to show them a login form. Smart Lock is backwards compatible from API 9
or Gingerbread. In a case study, one company found 20% reduction in login
related support request by adding Smart Lock and it really warms up the user
experience.

Android O adds the Autofill framework. Autofill works just like in Chrome,
offering to save form contents when users enter their passwords and other
information. As a developer, you can get ready by adding autofillHints and
setting up a digital asset link between their website and Android application.
This is a json file that you holds on your web server, which allows Autofill
services share autofill data between your app and android app.

But how do you move content that isn't behind the login? For example, in Gmail
we show a welcome to our new users. You expect this to not repeat on your new
phone, and it doesn't. We use our cloud backup service to build this
functionality. And because offering a warm welcome is important to Android
users, we offer it to Androd apps for free. Auto Backup saves all of your apps
changed files, up to 25mb nightly. When your app installs again, the files are
downloaded before it launches. Restores aren't limited to play installs, and happen even when you sideload from Android Studio or ADB. To learn more about configuring Auto Backup, including how to exclude files, check out the documentation on developer.android.com. Remember, Auto Backup is not a good place to store information like passwords, credit card numbers and definitely not for storing sensitive information, like medical records or financial data. If your app already has a different way to transfer settings to a new phone, check to make sure you're transfering all of the settings. It's easy to forget a few, and users really want that warm welcome.

#### References
***
[Youtube Video](https://www.youtube.com/watch?v=t3q4hEXcosY)

[Google Sign-in](https://goo.gl/GbfqgP)

[Smart Lock](https://developers.google.com/identity/smartlock-passwords/android/)

[Autofil](https://goo.gl/nZuPgM)

[Auto Backup](https://goo.gl/ekwCGY)
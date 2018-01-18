### Account Transfer API for Android Apps

#### Content
***
Retaining users in mobile presents a unique challenge. Users enter their password once and stay logged in for years until they get a new phone. To help your users out when they upgrade their phone, the Account Transfer API let you copy credentials, such as access tokens or passwords from their old phone. No need for an internet server. The credentials move over an encrypted bluetooth connection or a phone-to-phone cable. Accounts are transfered between two phones with different requirements. 

+ The old phone must be running a recent version of Google Play services on Ice Cream Sandwich or higher;

+ While the new one must be running Oreo or higher.

The Account Transfer API requires your app provide an authenticator for AccountManager. This will show your accounts on the main Android Settings screen and it can help you securely store account credentials.

So, how do you support Account Transfer?

The user starts an account transfer when they setup their new phone. Your app will receive three broadcast and you should create a foreground service on both devices in response to these broadcasts(ACTION_START_ACCOUNT_EXPORT, ACTION_ACCOUNT_EXPORT_DATA_AVAILABLE, ACTION_ACCOUNT_IMPORT_DATA_AVAILABLE). The old phone gets an export data available broadcast - ACTION_ACCOUNT_EXPORT_DATA_AVAILABLE. Then, from your foreground service, you send account details via sendData(). The API will send any binary data. Though, we suggest creating a version protocol, such as a version JSON dictionary or a protocol buffer. That way, older version will be able to communicate with your latest version. 

The new phone sees an import data available broadcast - ACTION_ACCOUNT_IMPORT_DATA_AVAILABLE. You retrieve the account details via retrieveData() in your foreground data service. Once you pass the account details, call AccountManager.addAccountExplicitly() to save the account. If your app does not ship installed on their new phone, don't worry and you can still get the data. It will be stored in the new phone until Google play re-installs your app. On first launch, you can then call retrieve data to fetch the account credentials. The API can show a user challenge, though, this is not recommended. During an account transfer, your user is excited to use their new devices and it is not using your app. Instead, if you require more verification of the phone, for example, two-factor auth, we recommend prompting them on the next app launch. That's it. 

After the transfer completes, the user's account will be ready. For the entire API, follow the links in description and look at the sample which has a working implementation to try on your phone. For other ways to increase metrics for both new and existing accounts, check out our earlier video covering Google sign-in and Smart Lock to optimize your logins.

#### References
***
[Youtube Video](https://www.youtube.com/watch?v=Gtp2oHGBqNY)

[Account Transfer API](https://goo.gl/7r63Pv)

[Sample Code](https://goo.gl/5wLPYk)
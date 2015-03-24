App Content Share with Deeplink for Android
===========================================

This guide will help you integrate content sharing with deeplinking into your app. We will also cover several customizations you can perform. The sections are as follows:

1. [Dashboard Configuration](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-app-content-share-with-deeplink.md#1-dashboard-configuration)
1. [Configuring Your App](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-app-content-share-with-deeplink.md#2-configuring-your-app)
1. [Sharing Content](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-app-content-share-with-deeplink.md#3-sharing-content)
1. [Routing to Content](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-app-content-share-with-deeplink.md#4-routing-to-content)
1. [Identify Your Influential Users](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-app-content-share-with-deeplink.md#5-identify-your-influntial-users)
1. [Analytics](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-app-content-share-with-deeplink.md#6-analytics)


## 1. Dashboard Configuration

To get started, create an account in [https://dashboard.branch.io/](https://dashboard.branch.io/). 

Navigate to the Settings page and fill in information in each of the fields, which at the very minimum includes:

1. App Name
1. Your Play Store / App Store information
1. URI Scheme

If this is your very first time, see these [instructions with graphics](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-quick-start.md#the-dashboard).


## 2. Configuring Your App

#### A. Integrating your app key

Now you'll need the API key that you created in the [Branch Dashboard](https://dashboard.branch.io/). 

Open up **strings.xml** and add your key as a String value.

```XML
<string name="bnc_app_key">your app key</string>
```

Now add a reference to the value that your just created in your **AndroidManifest.xml** file, within the Application element.

```XML

<application .. >
	
	<!-- Your existing activities, intent filters and associated attributes. -->
	<activity .. >
		..
	</activity>

	<!-- The reference to the string value that you created in strings.xml, with name as shown. -->
	<meta-data android:name="io.branch.sdk.ApplicationId" android:value="@string/bnc_app_key" />
	
</application>
```

[View in Android SDK Readme](https://github.com/BranchMetrics/Branch-Android-SDK/blob/master/README.md#add-your-app-key-to-your-project)

#### B. URI Scheme

Register an activity for direct deep linking (optional but recommended)

In your project's manifest file, you can register your app to respond to direct deep links (yourapp:// in a mobile browser) by adding the second intent filter block. Also, make sure to change **yourapp** to a unique string that represents your app name.

Typically, you would register some sort of splash activitiy that handles routing for your app.

```xml
<activity
	android:name="com.yourapp.SplashActivity"
	android:label="@string/app_name" >
	<intent-filter>
		<action android:name="android.intent.action.MAIN" />
		<category android:name="android.intent.category.LAUNCHER" />
	</intent-filter>

	<!-- Add this intent filter below, and change yourapp to your app name -->
	<intent-filter>
		<data android:scheme="yourapp" android:host="open" />
		<action android:name="android.intent.action.VIEW" />
		<category android:name="android.intent.category.DEFAULT" />
		<category android:name="android.intent.category.BROWSABLE" />
	</intent-filter>
</activity>
```

[View in Android SDK Readme](https://github.com/BranchMetrics/Branch-Android-SDK/edit/master/README.md)


#### C. Importing the SDK

Import the SDK as a Gradle dependency (for Android Studio):
* Right click on the main module within your project (this is called 'app' by default).
* Select **Open Module Settings**.
* Within the **Dependencies** tab, click the **+** button at the bottom of the window and select **Library Dependency**.
* Type *branch*, and hit the enter key to search Maven Central for the Branch SDK Library.
* Select the latest *io.branch.sdk.android:library* item listed and accept the changes.

See the [Android Quick Start Guide for more detail](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-quick-start.md) and a screencasted walkthrough. This guide also has many more options for downloading and importing the SDK.


#### D. Initialize SDK And Register Deep Link Routing Function

##### Init Session

Called in your splash activity where you handle. If you created a custom link with your own custom dictionary data, you probably want to know when the user session init finishes, so you can check that data. Think of this callback as your "deep link router". If your app opens with some data, you want to route the user depending on the data you passed in. Otherwise, send them to a generic install flow.

This deep link routing callback is called 100% of the time on init, with your link params or an empty dictionary if none present.

```java
@Override
public void onStart() {
	super.onStart();

	// Your app key can be retrieved on the [Settings](https://dashboard.branch.io/#/settings) page of the dashboard
	Branch branch = Branch.getInstance(getApplicationContext());
	branch.initSession(new BranchReferralInitListener(){
		@Override
		public void onInitFinished(JSONObject referringParams, BranchError error) {
			if (error == null) {
				// params are the deep linked params associated with the link that the user clicked before showing up
				// params will be empty if no data found

				// here is the data from the example below if a new user clicked on Joe's link and installed the app
				String name = referringParams.getString("user"); // returns Joe
				String profileUrl = referringParams.getString("profile_pic"); // returns https://s3-us-west-1.amazonaws.com/myapp/joes_pic.jpg
				String description = referringParams.getString("description"); // returns Joe likes long walks on the beach...

				// route to a profile page in the app for Joe
				// show a customer welcome
			} else {
				Log.i("MyApp", error.getMessage());
			}
		}
	}, this.getIntent().getData(), this);
}
```

##### Close Session

**Required:** this call will clear the deep link parameters when the app is closed, so they can be refreshed after a new link is clicked or the app is reopened. **You must call closeSession in every activity where Branch is initialized.** 

```java
@Override
public void onStop() {
	super.onStop();
	Branch.getInstance(getApplicationContext()).closeSession();
}
```

Again, we're emphasize this to save you trouble down the road -- **you need initSession/closeSession in every activity where you want to make use of Branch.**


## 3. Sharing Content

We will start with the most basic implementation of sharing. Let's say the content you want to share is a picture with a caption. We recommend always setting the feature and channel.

```java
JSONObject params = new JSONObject();
try {
    params.put("referringUsername", "Mario");
    params.put("referringUserId", "1234");
    params.put("pictureId", "987666");
    params.put("pictureURL", "http://yoursite.com/pics/987666");
    params.put("pictureCaption", "The princess and the plumber");
} catch (JSONException ex) { }
Branch branch = Branch.getInstance(getApplicationContext());
branch.getShortUrl("SMS", "share", null, params, new BranchLinkCreateListener() {
    @Override
    public void onLinkCreate(String url, Branch.BranchError error) {
        if (error == null) {
            // show the link to the user or share it immediately
        } else {
            Log.i("MyApp", error.getMessage());
        }
    }
});
```

If your app already has sharing functionality, simply replace an existing link to this piece of content with this link. Skip down to the next section, "4. Routing to Content."


#### Optional: Social Media (OG) tags

The example below has additional tags that customize the appearance of your links on social media. 

```java
JSONObject params = new JSONObject();
try {
    params.put("referringUsername", "Mario");
    params.put("referringUserId", "1234");
    params.put("pictureId", "987666");
    params.put("pictureURL", "http://yoursite.com/pics/987666");
    params.put("pictureCaption", "The princess and the plumber");

    // customize the display of the Branch link
    params.put("$og_image_url", "http://yoursite.com/pics/987666");
    params.put("$og_title", "Mario's Recent Picture");
    params.put("$og_description", "The princess and the plumber");
} catch (JSONException ex) { }
Branch branch = Branch.getInstance(getApplicationContext());
branch.getShortUrl("Facebook", "share", null, params, new BranchLinkCreateListener() {
    @Override
    public void onLinkCreate(String url, Branch.BranchError error) {
        if (error == null) {
            // show the link to the user or share it immediately
        } else {
            Log.i("MyApp", error.getMessage());
        }
    }
});
```

Now any social media sites that make use of titles, descriptions or images will display a rich preview of your app's content to users.

A **full list** of these tags can be found [here](https://github.com/BranchMetrics/Branch-iOS-SDK/blob/master/README.md#generate-tracked-deep-linking-urls-pass-data-across-install-and-open).

You should now help the user share this on Facebook, via SMS, or through whichever channel(s) you choose.


## 4. Routing to Content

Branch is beautiful because it allows deeplinking directly to content -- even if the user clicking the link does not have the app installed! Upon opening the app, a user can be directed straight to content and even an individually-personalized experience. 

The following implementation can tell if a user wanted to view a picture of an existing monster - even if the user just installed the app and this is the first open! If the user clicked on a Branch link with the parameter _monster_ attached, the application redirects to a screen to view the picture. In addition, this user can be shown a personalized message, such as the following: "Thanks for checking out our app. Let's view the picture that Mario just shared with you." Otherwise the default view controller is shown. Obviously routing logic is heavily implementation-specific, so the code below is just an example. See our Android sample project [Branchster](https://github.com/BranchMetrics/Branchster-Android) for the full example on routing.

```java
private static final String TAG = "MyActivity";

protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    branch = Branch.getInstance(this.getApplicationContext());
    branch.initSession(new BranchReferralInitListener() {
        @Override
        public void onInitFinished(JSONObject referringParams, BranchError error) {
            if (error != null) {
                Log.e(TAG, "branch init failed");
                Intent i = new Intent(getApplicationContext(), MonsterViewerActivity.class);
                startActivity(i);
            }
            else {
                Log.i(TAG, "branch init complete!");
                try {
                    MonsterPreferences prefs = MonsterPreferences.getInstance(getApplicationContext());
                    Intent i;
                    if (referringParams.has("monster")) {
                        prefs.setMonsterName(referringParams.getString("monster_name"));
                        prefs.setFaceIndex(referringParams.getInt("face_index"));
                        prefs.setBodyIndex(referringParams.getInt("body_index"));
                        prefs.setColorIndex(referringParams.getInt("color_index"));
                        i = new Intent(getApplicationContext(), MonsterViewerActivity.class);
                    }
                    else {
                        if (prefs.getMonsterName() == null) {
                            prefs.setMonsterName("");
                            i = new Intent(getApplicationContext(), MonsterCreatorActivity.class);
                            // If no name has been saved, this user is new, so load the monster maker screen
                        }
                        else {
                             i = new Intent(getApplicationContext(), MonsterViewerActivity.class);
                        }
                    }
                startActivity(i);
                }
                catch (JSONException e) {
                    e.printStackTrace();
                }
            }
        }
    }, this.getIntent().getData(), this);
}
```

## 5. Identify Your Influntial Users

#### Two Lines of Code

Branch automatically tracks unique devices. However, to make full use of our powerful API, you should also identify users with form of unique identification your app uses. The usefulness of this is hard to understate. When making future queries, when scanning through data on the dashboard, and when combing through anything you choose to export, you'll see your app's uniqueId alongside the Branch-provided ID.

The good news is that your app only needs the addition of two lines of code.

Add a `setIdentity` call wherever you create or login a user.

To identify a user, just call:
```java
Branch.getInstance(getApplicationContext()).setIdentity("your user id"); // your user id should not exceed 127 characters
```

If you provide a logout function in your app, be sure to clear the user when the logout completes. This will ensure that all the stored parameters get cleared and all events are properly attributed to the right identity.

**Warning** this call will clear the referral credits and attribution on the device.

Add a `logout` call anywhere you allow the user to logout.

```java
Branch.getInstance(getApplicationContext()).logout();
```

#### One Powerful Dashboard

As a side effect of what you've set up so far, you can easily see which users are your top influencers. If you navigate to the Dashboard's Referrals page and click on the [Influencers tab](https://dashboard.branch.io/#/referrals/influencers), you'll see who is referring the most users. Their Branch-determined Id is shown, along with any identification that you set during the setIdentity: call discussed above. The app-specific User ID is displayed on the left.

![Influencers](https://s3-us-west-1.amazonaws.com/branch-guides/influencers.png)

This tab is considerably more useful if you set an identity per the line of code above. Being able to use your app's actual user ID's rather than Branch-generated ones is vital for joining with your app's other data sets.


## 6. Analytics

Branch empowers advanced analytics, including tracking custom events and creating funnels.

#### Tracking Events

To track any event that you would like to use in funnels and other analytics, simply call one line of code:

```java
Branch branch = Branch.getInstance(getApplicationContext());
branch.userCompletedAction("sent SMS"); // your custom event name should not exceed 63 characters
```

You can modify this to contain additional state, such as:

```java
Branch branch = Branch.getInstance(getApplicationContext());
branch.userCompletedAction("your_custom_event", (JSONObject)appState); // same 63 characters max limit
```

The above JSONObject appState could be used later to distinguish between users based on various characteristics. 


#### Funnels

To set up funnels on the Dashboard, at the bottom of the [Summary](http://dashboard.branch.io/#) page, choose "+ Add your first conversion funnel" and give it a name, such as "SMS shares after open". Now, choose "open" as the first event and "sent SMS" as the second event. That's it!

This is a rather trivial example since we only have one custom event. Better use cases involve tracking users through a multi-stage signup process, or from completing one custom event to completing a second. 

Another great custom event to track would be when the user enters the app to view a specific picture, as discussed in section 4 on routing. You could add this single line, which should appear immediately after the comment below:

```java
Branch branch = Branch.getInstance(getApplicationContext());
branch.userCompletedAction("your_custom_event", (JSONObject)appState); // same 63 characters max limit
```

In JSONObject appState, set any key-value pairs that will be useful, such as "referringUserId" or "pictureId".


#### If you want it, you can track it

This only scratches the tip of the iceberg as far as analytics is concerned--it's just enough to get you started. 

Log into the Dashboard and play around on the various tabs. If anything is unclear, shoot us an email at support@branchmetrics.io.


## Feedback

Please let us know what other advanced topics you would like to see and we will add them to this guide. For suggestions or questions, please email support@branchmetrics.io.
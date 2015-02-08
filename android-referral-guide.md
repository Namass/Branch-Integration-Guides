Android Referral Program Guide
=========================
This quick start guide shows you how to leverage Branch links to build a powerful referral system for your app.

# Table of Contents

1. [What Branch can do](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#what-branch-does)
1. [Referral system design considerations](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#referral-system-design-considerations)
1. [Basic Account Registration](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#basic-account-registration)
1. [SDK Initialization](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#sdk-initialization)
1. [Event Tracking and Reward Rules](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#event-tracking-and-reward-rules)
1. [Link Creation for User Attribution](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#link-creation-for-user-attribution)
1. [Identity Management for Influencer Tracking](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#identity-management-for-influencer-tracking)
1. [Testing Considerations](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#testing-considerations)
1. [Advanced: Coupon/Referral Codes](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#advanced-couponreferral-codes)
1. [Advanced: Extending Referrals to the Web](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#advanced-extending-referrals-to-the-web)


# What Branch Does

For a referral system, Branch provides:

*_- User attribution through a tracking, download URL_*
In short, we tell you when an existing user who you empower with a Branch link, drives a new install.

*_- (optional) Custom rewarding rules tied to events (install, signup, purchase, etc) in app_*
We allow you to tie reward events to special 

*_- (optional) User reward tracking/storage (integer balance)_*
We leave the actual user facing rewarding to you, but we store how many credits have been earned through our reward rules. This makes it easy so that you can just check the balance of credits in the app from us, give the user some reward, then clear the credit balance on our server.

*_- (optional) Credit transaction history_*
At any time, via API or SDK, you can retrieve the full credit history of the user

*_- (optional) Coupon codes for each user_*
You can use our system to generate referral codes and redeem them through our system. We track which user owns the code and gets rewarded when a new user redeems it.

## High Level Examples

You can use just the URLs to track who was the referrer or you can use the entire credit storage and referral code solution as well.

**Example _just-the-links_ implementation:**

- Create Branch links for each user in the app
	- bundle user ID into the dictionary
- Any user who installs from this link can access the original, inviting user ID
- You can handle rewarding.

**Example _full-credit-storage_ implementation:**

- Create Branch links for each user in the app
- Setup a reward rule to give 2000 credits to any referred user who installs from a Branch link
- Setup a reward rule to give 4000 credits to the referring user
- Retrieve credit balance from Branch server
- Apply credits to user’s order
- Redeem the credits from the Branch server

# Referral System Design Considerations

Here are a couple questions to ask yourself when you are starting to build a referral system:

*_Do I want Branch to track credit balances so I don't have to?_*
It's possible to just use us for the links and user attribution. If you're just planning to use the links, you only need section [4](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#sdk-initialization) and [6](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#link-creation-for-user-attribution) of this guide

*_Do I want to reward both users, the user who drove the new install or just the new user, and what will the reward be?_*
You'll want to decide this upfront, as it will play a critical role in the ultimate design of the system. With respect to Branch, you'll need to create a reward rule ([here](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#event-tracking-and-reward-rules)) for each person receiving a reward. 

*_Do I want to reward only fresh installs or existing users as well_*
If you want to reward only fresh installs, you'll want to tie our rewards to the 'install' action. If you want to reward both users, you'll want to tie the reward rule to the 'referred session' action. See [here](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#event-tracking-and-reward-rules) for instructions on configuring these reward rules.

If you are planning to allow existing users to be rewarded, you'll want to set the isReferrable boolean argument to true in the initSession call for Branch. This tells us that a pre-existing user is eligible for referral if they were to click on a Branch link and begin an app session. Please see section [4](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#sdk-initialization) for more info about this.

# Basic Account Registration

Our dashboard is the starting point for adding apps as well as tracking users of your app. 

To get started, point your browser to [https://dashboard.branch.io/](https://dashboard.branch.io/). If you haven't created an account before, you can signup and get taken through the basic setup right away. If you've signed up already, simply navigate to the [Summary](https://dashboard.branch.io/#) page and click the dropdown button in the top right. Choose "Create new app."

![Dashboard Screenshot](https://s3-us-west-1.amazonaws.com/branch-guides/2_dashboard.png)

You will be prompted to enter a name for your new app. Do so and press "Create."

![Dashboard Screenshot](https://s3-us-west-1.amazonaws.com/branch-guides/3_create_new_app.png)

Navigate to the Settings page. Scroll down to App Store Information and search for your app by name--this is the same name listed on Google Play. With this information, Branch will automatically redirect users without your app installed on their devices to the App Store.

In the case that your app cannot be found on the Play Store (e.g. if you are still in development, or you're not listed in the US Play stores), you can also enter a custom URL by choosing "Custom URL to APK File". If you choose this option, Chrome will not able able to properly open up your app if it's already installed unless you append '?id=you.package.name' to your URL.

# SDK Initialization

### Step 0: Install library project

Download JAR file from here:
https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-SDK.zip

The testbed project:
https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-TestBed.zip

Or just clone [this project](https://github.com/BranchMetrics/Branch-Android-SDK)!

### Step 1: Register an activity for direct deep linking (optional but recommended)

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

### Step 2: Add your app key to your project

After you register your app, your app key can be retrieved on the [Settings](https://dashboard.branch.io/#/settings) page of the dashboard. Now you need to add it to your project.

1. Open your res/values/strings.xml file
1. Add a new string resource with the name "bnc_app_key" and value as your app key
    ```xml
    <resources>
        <!-- Other existing resources -->

        <!-- Add this string resource below, and change "your app key" to your app key -->
        <string name="bnc_app_key">"your app key"</string>
    </resources>
    ```

1. Open your AndroidManifest.xml file
1. Add the following new meta-data
    ```xml
    <application>
        <!-- Other existing entries -->

        <!-- Add this meta-data below; DO NOT changing the android:value -->
        <meta-data android:name="io.branch.sdk.ApplicationId" android:value="@string/bnc_app_key" />
    </application>
    ```

### Step 3: Initialization in Launcher Activity

Branch must be initialized on app open in order to check whether the user had just clicked a link or not. This first init call is critical as it's where a number of things happen. Here's a list with an explanation of each:

1. _initSession_ an asynchronous call to Branch to check if a user had just clicked a Branch link. It's about a 40 ms server call. It will automatically trigger events on the Branch dashboard for your app. The possible events that are triggered are:

- _install_
- _open_
- _referred session_

Either _install_ or _open_ will be triggered every single time the app opens, and _referred session_ will be triggered in addition to the previous two if a user had just clicked a link. Do not set up conversion funnels with _install_ or _open_ before or after _referred session_ because they can be saved out of order and they will result in funky data.

Two scenarios must be true in order for _install_ to be called, and _open_ will be called in all other scenarios:

	1) It must be the first install for that hardware id. A uninstall/reinstall is considered an open. (for testing, use setDebug to reset the hardware id every time)
	2) It must not be an app update. We compare the APK creation date to the modification date, to ensure updates are conisdered opens

2. You register a _deeplink handler_ BranchReferralInitListener, which will be called 100% of the time (even if a poor connection). When this callback is called, it will pass in the JSONObject of deep link parameters associated with the link a user just clicked or an empty JSONObject if no link was clicked.

3. If you plan to use Branch to track credits, you can customize your user's referral eligibility using the argument *isReferrable*. You can call this version of initSession to do so: initSession(BranchReferralInitListener callback, boolean *isReferrable*, Uri data, Activity activity). By default, we only consider a user eligible for referrals if they are a _fresh install_. If you would like ALL users, installs and opens, to be considered eligible for referrals, then you'll want to specify *true* for the isReferrable argument. 

Here is the code to initialize your app with Branch:

```java
@Override
public void onStart() {
	super.onStart();

	// Your app key can be retrieved on the [Settings](https://dashboard.branch.io/#/settings) page of the dashboard
	Branch branch = Branch.getInstance(getApplicationContext());
	branch.initSession(new BranchReferralInitListener(){
		@Override
		public void onInitFinished(JSONObject referringParams, Branch.BranchError error) {
			if (error == null) {
				// params are the deep linked params associated with the link that the user clicked before showing up
				// params will be empty if no data found

				
				// User the params to: 
				// 1. route to specific destination in the app
				// 2. handle your own rewarding if you'd like
				// 3. show a custome welcome from the referring user
			} else {
				Log.i("MyApp", error.getMessage());
			}
		}
	}, this.getIntent().getData(), this);
	// if you want to specify isReferrable, then comment out the above line and uncomment this line:
	// }, true, this.getIntent().getData(), this);
}
```

This code is also required in the same activity where you called the above to notify Branch that the app has been closed.

```java
@Override
public void onStop() {
	super.onStop();
	Branch.getInstance(getApplicationContext()).closeSession();
}
```

### Step 4: Session Management

You _must *initialize and close* a session in every activity_ that you plan to use Branch in. It's recommended that you go throughout the project and just add it everywhere so as not to accidentally make a mistake where you'll try to call Branch without an open session. This is required for us to know when a session should be closed out to be eligible for a new deep link. Below is the code you should use elsewhere:


```java
@Override
public void onStart() {
	super.onStop();
	Branch.getInstance(getApplicationContext()).initSession();
}

@Override
public void onStop() {
	super.onStop();
	Branch.getInstance(getApplicationContext()).closeSession();
}
```

# Event Tracking and Reward Rules

If you decide to track user credits through the Branch platform, then you must configure reward rules on the dashboard and tie them to events that occur in your app. Think of the system like this, your users are constantly completing a steady stream of actions in your app: 'install', 'signup', 'product_purchased'. Using the Branch reward rules, you tie reward events to specific actions. Then, with every event that is saved in Branch, we check automatically if that event is eligible for credits based on the rules that you configured, then deposit the credits if so. It's very powerful.

### Tracking Events

Tracking events through the Branch platform has many benefits. First off, you can create powerful conversion funnels to get a sense of key flows in your app like so:

![Conversion funnel example](https://s3-us-west-1.amazonaws.com/branch-guides/referral_event_conversion.png)

Please note, that the events 'install', 'open' and 'referred session' are automatically created by the Branch service when you call initSession. Please see the [above section]()

In order to track events in your app, you must use the following code:

```java
Branch branch = Branch.getInstance(getApplicationContext());
branch.userCompletedAction("product_purchased"); // your custom event name should not exceed 63 characters
```

OR if you want to store some state with the event, you can specify a JSONObject of additional key/value pairs with that event. Please note that this is where you specify the additional parameters that can help create more advanced rewarding rules based on the the _filter_ options described in the section below.

```java
JSONObject appState = new JSONObject();
appState.set("eligible", "yes");
appState.set("favorite_color", "blue");

Branch branch = Branch.getInstance(getApplicationContext());
branch.userCompletedAction("product_purchased", appState); // same 63 characters max limit
```

### Tie Reward Rules to Events

Now that you've populated your app with custom events to track, you can create the reward rules that will properly

![Reward rule start](https://s3-us-west-1.amazonaws.com/branch-guides/referral_rewards.png)

![Reward rule create](https://s3-us-west-1.amazonaws.com/branch-guides/reward_rule_creation.png)

![Reward rule filter](https://s3-us-west-1.amazonaws.com/branch-guides/reward_rule_filter.png)

# Link Creation for User Attribution

# Identity Management for Influencer Tracking

# Testing Considerations

# Advanced: Coupon/Referral Codes

# Advanced: Extending Referrals to the Web

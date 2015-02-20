iOS Referral Program Guide
=========================
This quick start guide shows you how to leverage Branch links to build a powerful referral system for your app.

# Table of Contents

1. [What Branch can do](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#what-branch-does)
1. [Referral system design considerations](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#referral-system-design-considerations)
1. [Basic Account Registration](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#basic-account-registration)
1. [SDK Initialization](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#sdk-initialization)
1. [Event Tracking and Reward Rules](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#event-tracking-and-reward-rules)
1. [Link Creation for User Attribution](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#link-creation-for-user-attribution)
1. [Identity Management for Influencer Tracking](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#identity-management-for-influencer-tracking)
1. [Credit Retrieval and Rewarding](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#credit-retrieval-and-rewarding)
1. [Testing Considerations](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#testing-considerations)
1. [Advanced: Extending Referrals to the Web](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#advanced-extending-referrals-to-the-web)
1. [Advanced: Coupon/Referral Codes](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#advanced-couponreferral-codes)


# What Branch Does

For a referral system, Branch provides:

**_- User attribution through a tracking, download URL_**
In short, we tell you when an existing user who you empower with a Branch link, drives a new install.

**_- (optional) Custom rewarding rules tied to events (install, signup, purchase, etc) in app_**
We allow you to tie reward events to special 

**_- (optional) User reward tracking/storage (integer balance)_**
We leave the actual user facing rewarding to you, but we store how many credits have been earned through our reward rules. This makes it easy so that you can just check the balance of credits in the app from us, give the user some reward, then clear the credit balance on our server.

**_- (optional) Credit transaction history_**
At any time, via API or SDK, you can retrieve the full credit history of the user

**_- (optional) Coupon codes for each user_**
You can use our system to generate referral codes and redeem them through our system. We track which user owns the code and gets rewarded when a new user redeems it.

## High Level Examples

You can use just the URLs to track who was the referrer or you can use the entire credit storage and referral code solution as well.

**Example _just-the-links_ implementation:**

- Create Branch links for each user in the app
	- bundle user ID into the dictionary
- Any user who installs from this link can access the original, inviting user ID
- You can handle rewarding.

**Example _enhanced-existing-referral-code_ implementation:**

- Create Branch links for each user in the app
	- bundle in legacy referral code for that user
- Any user who installs from this link can access the original, inviting user's referral code
- Automatically prefill referral code into the field for the user
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

**_Do I want Branch to track credit balances so I don't have to?_**
It's possible to just use us for the links and user attribution. If you're just planning to use the links, you only need section [4](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#sdk-initialization) and [6](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#link-creation-for-user-attribution) of this guide

**_Do I want to reward both users, the user who drove the new install or just the new user, and what will the reward be?_**
You'll want to decide this upfront, as it will play a critical role in the ultimate design of the system. With respect to Branch, you'll need to create a reward rule ([here](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#event-tracking-and-reward-rules)) for each person receiving a reward. 

**_Do I want to reward only fresh installs or existing users as well_**
If you want to reward only fresh installs, you'll want to tie our rewards to the 'install' action. If you want to reward both users, you'll want to tie the reward rule to the 'referred session' action. See [here](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#event-tracking-and-reward-rules) for instructions on configuring these reward rules.

If you are planning to allow existing users to be rewarded, you'll want to set the isReferrable boolean argument to true in the initSession call for Branch. This tells us that a pre-existing user is eligible for referral if they were to click on a Branch link and begin an app session. Please see section [4](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#sdk-initialization) for more info about this.

# Basic Account Registration

Our dashboard is the starting point for adding apps as well as tracking users of your app. 

To get started, point your browser to [https://dashboard.branch.io/](https://dashboard.branch.io/). If you haven't created an account before, you can signup and get taken through the basic setup right away. If you've signed up already, simply navigate to the [Summary](https://dashboard.branch.io/#) page and click the dropdown button in the top right. Choose "Create new app."

![Dashboard Screenshot](https://s3-us-west-1.amazonaws.com/branch-guides/2_dashboard.png)

You will be prompted to enter a name for your new app. Do so and press "Create."

![Dashboard Screenshot](https://s3-us-west-1.amazonaws.com/branch-guides/3_create_new_app.png)

Navigate to the Settings page. Scroll down to App Store Information and search for your app by name--this is the same name listed on the App Store. With this information, Branch will automatically redirect users without your app installed on their devices to the App Store.

In the case that your app cannot be found on the App Store (e.g. if you are still in development, or you're not listed in the US Play stores), you can also enter a custom URL by choosing "Custom URL to TestFlight/Other Host". 

# SDK Initialization

### Step 0: Install library project

#### Available in CocoaPods

Branch is available through [CocoaPods](http://cocoapods.org), to install it simply add the following line to your Podfile:

    pod "Branch"

#### Or download the raw files

Download code from here:
https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-SDK.zip

The testbed project:
https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-TestBed.zip

Or just clone [this project](https://github.com/BranchMetrics/Branch-iOS-SDK)!

### Step 1: Register an activity for direct deep linking (optional but recommended)

You can register your app to respond to direct deep links (yourapp:// in a mobile browser) by adding a URI scheme in the YourProject-Info.plist file. Also, make sure to change **yourapp** to a unique string that represents your app name.

1. In Xcode, click on YourProject-Info.plist on the left.
1. Find URL Types and click the right arrow. (If it doesn't exist, right click anywhere and choose Add Row. Scroll down and choose URL Types)
1. Add "yourapp", where yourapp is a unique string for your app, as an item in URL Schemes as below:

![URL Scheme Demo](https://s3-us-west-1.amazonaws.com/branchhost/urlScheme.png)

Alternatively, you can add the URI scheme in your project's Info page.

1. In Xcode, click your project in the Navigator (on the left side).
1. Select the "Info" tab.
1. Expand the "URL Types" section at the bottom.
1. Click the "+" sign to add a new URI Scheme, as below:

![URL Scheme Demo](https://s3-us-west-1.amazonaws.com/branchhost/urlType.png)

### Step 2: Add your app key to your project

After you register your app, your app key can be retrieved on the [Settings](https://dashboard.branch.io/#/settings) page of the dashboard. Now you need to add it to YourProject-Info.plist (Info.plist for Swift).

1. In plist file, mouse hover "Information Property List" which is the root item under the Key column.
1. After about half a second, you will see a "+" sign appear. Click it.
1. In the newly added row, fill in "bnc_app_key" for its key, leave type as String, and enter your app key obtained in above steps in its value column.
1. Save the plist file.

### Step 3: Initialization in Launcher Activity

Branch must be initialized on app open in order to check whether the user had just clicked a link or not. This first init call is critical as it's where a number of things happen. Here's a list with an explanation of each:

1) **_initSession_** an asynchronous call to Branch to check if a user had just clicked a Branch link. It's about a 40 ms server call. It will automatically trigger events on the Branch dashboard for your app. The possible events that are triggered are:

- _install_
- _open_
- _referred session_

Either _install_ or _open_ will be triggered every single time the app opens, and _referred session_ will be triggered in addition to the previous two if a user had just clicked a link. Do not set up conversion funnels with _install_ or _open_ before or after _referred session_ because they can be saved out of order and they will result in funky data.

Two scenarios must be true in order for _install_ to be called, and _open_ will be called in all other scenarios:

- It must be the *first install* for that IDFA. A uninstall/reinstall is considered an open. (for testing, use setDebug to reset the IDFA every time)
- It must *not be an app update*. We compare the bundle creation date to the modification date, to ensure updates are conisdered opens

2) You register a **_deeplink handler_** block, which will be called 100% of the time (even if a poor connection). When this callback is called, it will pass in the NSDictionary of deep link parameters associated with the link a user just clicked or an empty NSDictionary if no link was clicked.

3) If you plan to use Branch to track credits, you can customize your user's referral eligibility using the argument **_isReferrable_**. You can call this version of initSession to do so: initSessionWithLaunchOptions:launchOptions isReferrable:YES andRegisterDeepLinkHandler:. By default, we only consider a user eligible for referrals if they are a _fresh install_. If you would like ALL users, installs and opens, to be considered eligible for referrals, then you'll want to specify *YES* for the isReferrable argument. 

Here is the code to initialize your app with Branch:

##### Objective-C
```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
	// your other init code

	Branch *branch = [Branch getInstance];
	[branch initSessionWithLaunchOptions:launchOptions andRegisterDeepLinkHandler:^(NSDictionary *params, NSError *error) {	
	// if you want to specify isReferrable, then comment out the above line and uncomment this line:
	// [branch initSessionWithLaunchOptions:launchOptions isReferrable:YES andRegisterDeepLinkHandler:^(NSDictionary *params, NSError *error) {	
        if (!error) {
            // params are the deep linked params associated with the link that the user clicked before showing up
			// params will be empty if no data found

			
			// User the params to: 
			// 1. route to specific destination in the app
			// 2. handle your own rewarding if you'd like
			// 3. show a custome welcome from the referring user
        }
	}];
}
```

```objc
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation {
	// pass the url to the handle deep link call
	// if handleDeepLink returns YES, and you registered a callback in initSessionAndRegisterDeepLinkHandler, the callback will be called with the data associated with the deep link
	if (![[Branch getInstance] handleDeepLink:url]) {
		// do other deep link routing for the Facebook SDK, Pinterest SDK, etc
	}
    return YES;
}
```

##### Swift
```swift
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
    // your other init code
	
    let branch: Branch = Branch.getInstance()
    branch.initSessionWithLaunchOptions(launchOptions, andRegisterDeepLinkHandler: { params, error in
        if (error == nil) {
            // params are the deep linked params associated with the link that the user clicked before showing up
			// params will be empty if no data found

			
			// User the params to: 
			// 1. route to specific destination in the app
			// 2. handle your own rewarding if you'd like
			// 3. show a custome welcome from the referring user
        }
    })
        
    return true
}
```

```swift
func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
    // pass the url to the handle deep link call
    // if handleDeepLink returns true, and you registered a callback in initSessionAndRegisterDeepLinkHandler, the callback will be called with the data associated with the deep link
    if (!Branch.getInstance().handleDeepLink(url)) {
        // do other deep link routing for the Facebook SDK, Pinterest SDK, etc
    }
        
    return true
}
```

#### Retrieving Link Parameters Later On

##### The Initial Referring Params

If you ever want to access the original referring session params (the parameters passed in for the first referral event only - install only by default or first time if isReferrable is true), you can use this line. This is useful if you only want to reward users who newly installed the app from a referral link or something.

Additionally, if you track identities through the Branch system using [this section](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#identity-management-for-influencer-tracking). The first referring parameters will be retrieved when you call setIdentity and we find that that particular user id had been referred before.

For a particular user, these first referring parameters can never be cleared or updated.

```objc
NSDictionary *sessionParams = [[Branch getInstance] getLatestReferringParams];
```

##### The Current Session Params

Every time a link is clicked and the user begins an app session, that will be considered a 'referred session' and the link parameters will be available at any time in the following call. Once the user minimizes the app, the session will be closed and those parameters will be cleared.

```objc
NSDictionary *installParams = [[Branch getInstance] getFirstReferringParams];
```

# Event Tracking and Reward Rules

If you decide to track user credits through the Branch platform, then you must configure reward rules on the dashboard and tie them to events that occur in your app. Think of the system like this, your users are constantly completing a steady stream of actions in your app: 'install', 'signup', 'product_purchased'. Using the Branch reward rules, you tie reward events to specific actions. Then, with every event that is saved in Branch, we check automatically if that event is eligible for credits based on the rules that you configured, then deposit the credits if so. It's very powerful.

### Tracking Events

Tracking events through the Branch platform has many benefits. First off, you can create powerful conversion funnels to get a sense of key flows in your app like so:

![Conversion funnel example](https://s3-us-west-1.amazonaws.com/branch-guides/referral_event_conversion.png)

Please note, that the events _install_, _open_ and _referred session_ are automatically created by the Branch service when you call initSession. Please see the [above section](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#step-3-initialization-in-launcher-activity) for more details on these events

In order to track events in your app, you must use the following code:

```objc
Branch *branch = [Branch getInstance];
[branch userCompletedAction:@"product_purchase"]; // your custom event name should not exceed 63 characters
```

OR if you want to store some state with the event, you can specify a JSONObject of additional key/value pairs with that event. Please note that this is where you specify the additional parameters that can help create more advanced rewarding rules based on the the _filter_ options described in the section below.

```objc
NSMutableDictionary appState = [[NSMutableDictionary alloc] init];
[appState setObject:@"yes" forKey:@"eligible"];
[appState setObject:@"blue" forKey:@"favorite_color"];

Branch *branch = [Branch getInstance];
[branch userCompletedAction:@"product_purchase" withState:appState]; // same 63 characters max limit
```

### Tie Reward Rules to Events

Now that you've populated your app with custom events to track, you can create the reward rules to automatically add credits to a users. Click _Add a new rule_ to begin.

![Reward rule start](https://s3-us-west-1.amazonaws.com/branch-guides/referral_rewards.png)

Rule creation for credits is very intuitive and written in a form that makes it easy to understand such complex systems. In the example below, the 'referring users' will get an unspecified number of credits every time users they've referred have triggered a _product_purchased_ event.

If you also want to allow the user who was referred to get a reward, you'll want to create a new rule in addition to this one.

![Reward rule create](https://s3-us-west-1.amazonaws.com/branch-guides/reward_rule_creation.png)

#### Advanced & Optional: Credit Buckets

Buckets are a way for you to store special kinds of rewards. This is most applicable if you're planning to offer tiers of reward that may or may not be tied to dollars. For example, maybe you are a photo app thats to award different kinds of filters for special actions completed. Or perhaps you are a game that wants to give differentl levels away.

When you create the reward rule, you'll want to specify a credit type other than _default_ type of credit. You can create specify an unlimited number of credit types with whatever name you'd like. Any credits that are awarded from that rule will be deposited in the specified credit bucket. If left as is, all credits will be deposited into the _default_ bucket.

#### Advanced & Optional: Reward Rule Filters

Sometimes a simple event string is not enough for the complex types of systems you can dream up. For this reason, we added the ability to specify filters in addition to the basic reward rule string. This adds an additional filter on reward eligibility, tied to the event metadata that you specify in _userCompleteAction_ above.

In the example above, we added "eligible":"yes" and "favorite_color":"blue" to the event via the appState NSDictionary. While creating the rule, if you were to add "favorite_color":"red" into the dialog below, users that have a favorite_color of blue will not be eligible for the reward. Only users with a favorite_color of red will receive the credits.

![Reward rule filter](https://s3-us-west-1.amazonaws.com/branch-guides/reward_rule_filter.png)

#### Advanced & Optional: Use Webhooks to Notify Your Server of Referrals

For the sake of simplicity, I'll just point you to the [webhook integration guide](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/webhook-guide.md) that contains all of the details you'll need to receive referral notifications on your own server.

# Link Creation for User Attribution

This section covers the meat of using the Branch links to power your referral system. The core functionality revolves around the link, as it's the only piece necessary to do attribution and rewarding on the user level. Say goodbye to referral codes, as Branch links do the same thing _without user input_. The user only need to click on his or her friend's link and install the app to be considered referred.

When you create a Branch link with the SDK, it's automatically associated with that current user of the app. Branch handles all that complexity for you.

Branch links are extremely extensible, but here are a few considerations when you create it in the SDK:

- Make sure to label the links (feature, channel, etc) so that you can filter the *dashboard analytics*.
	- **_feature_** Since this is a referral program guide, specify BRANCH_FEATURE_TAG_REFERRAL for this argument
	- **_channel_** You'll want to specify the channel that the link will be shared on, eg 'facebook', 'twitter', 'sms' etc so that you can see which channels driving the most users
	- **_stage_** Use this if your users can access the feature at different stages in your app. This way you can see which stage is working best.

- Decide on what *custom deeplink data* you want to permanently associate with the Branch link that you create
	- Associate all data you have about the user (name, picture, etc) so that anyone who installs from the link will see their face
	- If you are enhancing an existing referral code system, stash the code in the link so it's retrievable for users who click the link

- Customize the *appearance* of a link on social media. You can customize the Facebook OG tags of each URL if you want to dynamically share content by using the following _optional keys in the data dictionary_. Please use this [Facebook tool](https://developers.facebook.com/tools/debug/og/object) to debug your OG tags!
	- These keys/values are specified in the *custom deeplink data* NSDictionary

	| Key | Value
	| --- | ---
	| "$og_title" | The title you'd like to appear for the link in social media
	| "$og_description" | The description you'd like to appear for the link in social media
	| "$og_image_url" | The URL for the image you'd like to appear for the link in social media
	| "$og_video" | The URL for the video 
	| "$og_url" | The URL you'd like to appear
	| "$og_app_id" | Your OG app ID. Optional and rarely used.

- Customize the *redirect functionality*
	- These keys/values are specified in the *custom deeplink data* NSDictionary

	| Key | Value
	| --- | ---
	| "$desktop_url" | Where to send the user on a desktop or laptop. By default it is the Branch-hosted text-me service
	| "$android_url" | The replacement URL for the Play Store to send the user if they don't have the app. Currently, Chrome does not support this override. _Only necessary if you want a mobile web splash_
	| "$ios_url" | The replacement URL for the App Store to send the user if they don't have the app. _Only necessary if you want a mobile web splash_

For more details on how to create links, see the [Branch link creation guide](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/url-creation-guide.md)

#### Create Urls Directly

```objc
// associate data with a link
// you can access this data from any instance that installs or opens the app from this link (amazing...)

NSMutableDictionary *params = [[NSMutableDictionary alloc] init];

[params setObject:@"Joe" forKey:@"user"];
[params setObject:@"https://s3-us-west-1.amazonaws.com/myapp/joes_pic.jpg" forKey:@"profile_pic"];
[params setObject:@"Joe likes long walks on the beach..." forKey:@"description"];

[params setObject:@"Joe's My App Referral" forKey:@"$og_title"];
[params setObject:@"https://s3-us-west-1.amazonaws.com/myapp/joes_pic.jpg" forKey:@"$og_image_url"];
[params setObject:@"Join Joe in My App - it's awesome" forKey:@"$og_description"];

[params setObject:@"http://myapp.com/desktop_splash" forKey:@"$desktop_url"];

Branch *branch = [Branch getInstance];
[branch getShortURLWithParams:params andChannel:@"sms" andFeature:BRANCH_FEATURE_TAG_REFERRAL andStage:@"post_purchase" andCallback:^(NSString *url, NSError *error) {
	// show the link to the user or share it immediately
}];
```

#### UIAcitivityView Share Sheet Url Creation

UIActivityView is the standard way of allowing users to share content from your app. A common use case is a user sharing a referral code, or a content URL with their friends. If you want to give your users a way of sharing content from your app, this is the simpelist way to implement Branch. **_It will automatically create URLs to embed in the message._**

Sample UIActivityView Share sheet:
![UIActivityView Share Sheet](https://s3-us-west-1.amazonaws.com/branchhost/iOSShareSheet.png )

The Branch iOS SDK includes a subclassed UIActivityItemProvider that can be passed into a UIActivityViewController, that will generate a Branch short URL and automatically tag it with the channel the user selects (Facebook, Twitter, etc.). The sample app included with the Branch iOS SDK shows a sample of this in ViewController.m:

```objc
// Setup up the content you want to share, and the Branch
// params and properties, as you would for any Branch link

NSString *shareString = @"Super amazing thing I want to share!";
NSString *defaultURL = @"http://lmgtfy.com/?q=branch+metrics";

NSMutableDictionary *params = [[NSMutableDictionary alloc] init];

[params setObject:@"Joe" forKey:@"user"];
[params setObject:@"https://s3-us-west-1.amazonaws.com/myapp/joes_pic.jpg" forKey:@"profile_pic"];
[params setObject:@"Joe likes long walks on the beach..." forKey:@"description"];

[params setObject:@"Joe's My App Referral" forKey:@"$og_title"];
[params setObject:@"https://s3-us-west-1.amazonaws.com/myapp/joes_pic.jpg" forKey:@"$og_image_url"];
[params setObject:@"Join Joe in My App - it's awesome" forKey:@"$og_description"];

[params setObject:@"http://myapp.com/desktop_splash" forKey:@"$desktop_url"];
    
// Branch UIActivityItemProvider
// No need to set the channel, that is done automatically based
// on the share activity the user selects
UIActivityItemProvider *itemProvider = [Branch getBranchActivityItemWithDefaultURL:defaultURL andParams:params andFeature:BRANCH_FEATURE_TAG_REFERRAL andStage:@"post_purchase"];

// Pass this in the NSArray of ActivityItems when initializing a UIActivityViewController
UIActivityViewController *shareViewController = [[UIActivityViewController alloc] initWithActivityItems:@[shareString, itemProvider] applicationActivities:nil];

// Present the share sheet!
[self.navigationController presentViewController:shareViewController animated:YES completion:nil];
```

# Identity Management for Influencer Tracking

Often, you might have your own user IDs, or want referral and event data to persist across platforms or uninstall/reinstall. It's helpful if you know your users access your service from different devices. This where we introduce the concept of an 'identity'.

It's _very helpful but not required_ to spend the time and properly label the identities of your users within Branch. This means that you call setIdentity with your user id to alias the Branch automatic identity with your own. There a numerous benefits to telling Branch what your identities are:

1. You can retrieve credit history, get credit balance and redeem credits via the [public API](https://github.com/BranchMetrics/Branch-Public-API) by specifying 'identity' with your user id.
2. The Branch credit management backend will work across devices (tablet and phone) and even on the web (if you use the [web SDK](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#advanced-extending-referrals-to-the-web))
3. You can track which of your users are the the top influencers (invite the most new users to your app). You can see the list of influencers by visiting the appropriate section of the dashboard. Here's an example of without/with aliasing your user id.

Influencer list WITHOUT calling setIdentity
![Influencer No User Id](https://s3-us-west-1.amazonaws.com/branch-guides/influencers_no_id.png)

Influencer list when properly calling setIdentity
![Influencer User Id](https://s3-us-west-1.amazonaws.com/branch-guides/influencers_with_id.png)

To identify a user, just call:
```objc
// previously identifyUser:
[[Branch getInstance] setIdentity:@"your user id"];	// your user id should not exceed 127 characters
```

### Logout and User Switching

If you provide a logout function in your app, be sure to clear the user when the logout completes. This will ensure that all the stored parameters get cleared and all events are properly attributed to the right identity.

**warning** this call will clear the referral credits and attribution on the device. Only call this when you're sure that the actual person using the app has changed.

```objc
[[Branch getInstance] logout];	// previously clearUser
```

# Credit Retrieval and Rewarding

If you've decided to have Branch store your credits, and you've set up everything correctly as listed above, then credits will be properly rewarded to your users that have earned them.

While the examples below show the details of how to implement the credit storage client side, it's not uncommon for you to want to retrieve and update these details on your server. For this, you can use the [public API](https://github.com/BranchMetrics/Branch-Public-API) if you've used setIdentity in the native app.

Please note that if you've tied rewards to installs, our backend processes the rewards in a queueing system that can sometimes result in a small delay between the install event being triggered and the actual credits being inserted into the user's balance. During normal times, this delay will be less than 500 ms, but in high loads, can take a second or more.

### Retrieve Credit Balances

Reward balances change randomly on the backend when certain actions are taken (defined by your rules), so you'll need to make an asynchronous call to retrieve the balance. This call is also available on the public API with [this call](https://github.com/BranchMetrics/Branch-Public-API#getting-credit-count). Here is the syntax:

```objc
[[Branch getInstance] loadRewardsWithCallback:^(BOOL changed, NSError *error) {
	// changed boolean will indicate if the balance changed from what is currently in memory

	// will return the balance of the current user's credits
	NSInteger credits = [[Branch getInstance] getCredits];
}];
```

### Redeem (Spend) Credits

We will store how many of the rewards have been deployed so that you don't have to track it on your end. In order to save that you gave the credits to the user, you can call redeem. Redemptions will reduce the balance of outstanding credits permanently. You can do this same functionality on the public API with [this call](https://github.com/BranchMetrics/Branch-Public-API#redeeming-credits).

```objc
// Save that the user has redeemed 5 credits
[[Branch getInstance] redeemRewards:5];
```

### Retrieve Credit History (for display purposes)

This call will retrieve the entire history of credits and redemptions from the individual user. Additionally, you can call this directly on the API with [this call](https://github.com/BranchMetrics/Branch-Public-API#getting-the-credit-history). To use this call, implement like so:

```objc
[[Branch getInstance] getCreditHistoryWithCallback:^(NSArray *history, NSError *error) {
    if (!error) {
        // process history
    }
}];
```

The response will return an array that has been parsed from the following JSON:
```json
[
    {
        "transaction": {
                           "date": "2014-10-14T01:54:40.425Z",
                           "id": "50388077461373184",
                           "bucket": "default",
                           "type": 0,
                           "amount": 5
                       },
        "referrer": "12345678",
        "referree": null
    },
    {
        "transaction": {
                           "date": "2014-10-14T01:55:09.474Z",
                           "id": "50388199301710081",
                           "bucket": "default",
                           "type": 2,
                           "amount": -3
                       },
        "referrer": null,
        "referree": "12345678"
    }
]
```
**referrer**
: The id of the referring user for this credit transaction. Returns null if no referrer is involved. Note this id is the user id in developer's own system that's previously passed to Branch's identify user API call.

**referree**
: The id of the user who was referred for this credit transaction. Returns null if no referree is involved. Note this id is the user id in developer's own system that's previously passed to Branch's identify user API call.

**type**
: This is the type of credit transaction

1. _0_ - A reward that was added automatically by the user completing an action or referral
1. _1_ - A reward that was added manually
2. _2_ - A redemption of credits that occurred through our API or SDKs
3. _3_ - This is a very unique case where we will subtract credits automatically when we detect fraud

# Testing Considerations

Testing a referral program can be challenging due to the fact that it requires two parties to complete. Here are a couple suggestions to ensure that you can test out the system to your satisfaction.

### Recommendation 1: Create A Duplicate Test App on the Branch Dashboard

At the top right of the dashboard, you have the option to create a new app. Use the same settings as your production app by choosing to copy an existing. Copy this new test app key into your native app so as to not pollute your production app's Branch data.

### Recommendation 2: Use setDebug To Simulate Fresh Installs

One challenge aspect testing Branch's service is simulating a fresh install. We intentionally add a lot of restrictions to prevent 'install' events from being triggered on app updates or uninstall/reinstall. This is discussed extensively in [this section](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-referral-guide.md#step-3-initialization-in-launcher-activity).

To simulate a brand new user being referred from our perspective:

0. use the setDebug call before you call initSession in the AppDelegate (see below)
1. Uninstall your test app
2. Clear your browser cookies
3. Click a link in the browser
4. Run your test app

```objc
	Branch *branch = [Branch getInstance];
	[Branch setDebug];
	[branch initSession......]
```

### Recommendation 3: Use different user ids

Another challenge is that we do a lot of magic (identity merging, etc) to ensure that a single user doesn't get unique rewards multiple times. You should beware that if you're calling setIdentity with a user id that had previously been rewarded a unique reward, you will not see his credit balance increment.

Make sure to use a different user id with the setIdentity call if you want to be sure credits will be properly rewarded.

# Advanced: Extending Referrals to the Web

This section of the guide is to be filled out, but here's a very simple description of what you'll have to do to get started:

1. Use the [Web SDK](https://github.com/BranchMetrics/Web-SDK) on your own site
2. Make sure to properly call identify with the same user ID that you called setIdentity in the app
3. Links, credits et all will be retrievable via the [Web SDK](https://github.com/BranchMetrics/Web-SDK) and will properly be attributed to the same user of the app.

# Advanced: Coupon/Referral Codes

We don't particularly recommend using coupon codes because they are not needed now that Branch links can properly attribute users just through link clicks alone, but we built out a parallel system that works with the links if you absolutely need to use codes. The only reason you should consider this is if you plan to do some promotion of the app with coupon codes on print media and television.

Here are the high level SDK methods to allow you to implement this:

### Step 1: Create referral code

Create a new referral code for the current user, only if this user doesn't have any existing non-expired referral code.

In the simplest form, just specify an amount for the referral code.
The returned referral code is a 6 character long unique alpha-numeric string wrapped inside the params dictionary with key @"referral_code".

**amount** _NSInteger_
: The amount of credit to redeem when user applies the referral code

```objc
// Create a referral code of 5 credits
[[Branch getInstance] getReferralCodeWithAmount:5
                                    andCallback:^(NSDictionary *params, NSError *error) {
                                        if (!error) {
                                            NSString *referralCode = [params objectForKey:@"referral_code"];
                                            // do whatever with referralCode
                                        }
                                    }
];
```

Alternatively, you can specify a prefix for the referral code.
The resulting code will have your prefix, concatenated with a 4 character long unique alpha-numeric string wrapped in the same data structure.

**prefix** _NSString*_
: The prefix to the referral code that you desire

```objc
// Create a referral code with prefix "BRANCH", 5 credits, and without an expiration date
[[Branch getInstance] getReferralCodeWithPrefix:@"BRANCH"   // prefix should not exceed 48 characters
                                         amount:5
                                    andCallback:^(NSDictionary *params, NSError *error) {
                                        if (!error) {
                                            NSString *referralCode = [params objectForKey:@"referral_code"];
                                            // do whatever with referralCode
                                        }
                                    }
];
```

You can also tune the referral code to the finest granularity, with the following additional parameters:

**expiration** _NSDate*_
: The expiration date of the referral code

**bucket** _NSString*_
: The name of the bucket to use. If none is specified, defaults to 'default'

**calculation_type**  _ReferralCodeCalculation_
: This defines whether the referral code can be applied indefinitely, or only once per user

1. _BranchUnlimitedRewards_ - referral code can be applied continually
1. _BranchUniqueRewards_ - a user can only apply a specific referral code once

**location** _ReferralCodeLocation_
: The user to reward for applying the referral code

1. _BranchReferreeUser_ - the user applying the referral code receives credit
1. _BranchReferringUser_ - the user who created the referral code receives credit
1. _BranchBothUsers_ - both the creator and applicant receive credit

```objc
[[Branch getInstance] getReferralCodeWithPrefix:@"BRANCH"   // prefix should not exceed 48 characters
				                         amount:5
				                     expiration:[[NSDate date] dateByAddingTimeInterval:60 * 60 * 24]
				                         bucket:@"default"
				                calculationType:BranchUniqueRewards
				                       location:BranchBothUsers
				                    andCallback:^(NSDictionary *params, NSError *error) {
				                        if (!error) {
				                            NSString *referralCode = [params objectForKey:@"referral_code"];
				                            // do whatever with referralCode
				                        }
			                       	}
];
```

### Step 2: Validate referral code

Validate if a referral code exists in Branch system and is still valid.
A code is vaild if:

1. It hasn't expired.
1. If its calculation type is uniqe, it hasn't been applied by current user.

If valid, returns the referral code JSONObject in the call back.

**code** _NSString*_
: The referral code to validate

```objc
[[Branch getInstance] validateReferralCode:code andCallback:^(NSDictionary *params, NSError *error) {
    if (!error) {
        if ([code isEqualToString:[params objectForKey:@"referral_code"]]) {
            // valid
        } else {
            // invaid (should never happen)
        }
    } else {
        NSLog(@"Error in validating referral code: %@", error.localizedDescription);
    }
}];
```

### Step 3: Apply referral code

Apply a referral code if it exists in Branch system and is still valid (see above). If the code is valid, returns the referral code NSDictionary in the call back. This will automatically deposit the number of credits into the user's Branch credit account.

**code** _NSString*_
: The referral code to apply

```objc
[[Branch getInstance] applyReferralCode:code andCallback:^(NSDictionary *params, NSError *error) {
    if (!error) {
        // applied. you can get the referral code amount from the params and deduct it in your UI.
    } else {
        NSLog(@"Error in applying referral code: %@", error.localizedDescription);
    }
}];
```

### Step 4: Redeem credits from code

We will store how many of the rewards have been deployed so that you don't have to track it on your end. In order to save that you gave the credits to the user, you can call redeem.  Redemptions will reduce the balance of outstanding credits permanently.

```objc
// Save that the user has redeemed 5 credits
[[Branch getInstance] redeemRewards:5];
```

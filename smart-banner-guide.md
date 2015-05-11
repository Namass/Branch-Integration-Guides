Smart Banner Guide for iOS
=====================================

This guide will help you integrate the smart banner for converting mobile web users to app installs while maintaining a seamless user experience. 

## 1. Create an account on the Branch Dashboard

To get started, create an account in [https://dashboard.branch.io/](https://dashboard.branch.io/). 

Navigate to the Settings page and fill in information in each of the fields, including your app store information, URI scheme, and company info. 


## 2. Setting up Branch in your app

### Installing the SDK

Full integration guides for iOS and Android are available at the following links with more features. 

The full Branch iOS docs can be found at [https://github.com/BranchMetrics/Branch-iOS-SDK](https://github.com/BranchMetrics/Branch-iOS-SDK), and the iOS illustrated guide can be found at [https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-quickstart.md](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-quickstart.md). 

The full Brand Android docs can be found at [https://github.com/BranchMetrics/Branch-Android-SDK](https://github.com/BranchMetrics/Branch-Android-SDK), and the Android illustrated guide can be found at [https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-quick-start.md](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-quick-start.md). 

#### -or- Download the raw files

Download iOS code from here:
[https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-SDK.zip](https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-SDK.zip)

The iOS testbed project:
[https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-TestBed.zip](https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-TestBed.zip)

Download JAR file from here: [https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-SDK.zip](https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-SDK.zip)

The Android testbed project: [https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-TestBed.zip](https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-TestBed.zip)


### Add your app key to your project

After you register your app, your app key can be retrieved on the [Settings](https://dashboard.branch.io/#/settings) page of the dashboard. 

##### iOS

For iOS, you need to add it to YourProject-Info.plist (Info.plist for Swift). Create a key called bnc_app_key, with string value: **yourAppID**

##### Android

For Android, add your Branch API Key to the **res/values/strings.xml** file in your project

```java
<resources>
    <!-- Other existing resources -->

    <!-- Add this string resource below -->
    <string name="bnc_app_key">87216441102696573</string>
</resources>
```

Update your **AndroidManifest.xml** file

```java
<application>
    <!-- Other existing entries -->

    <!-- Add this meta-data below -->
    <meta-data android:name="io.branch.sdk.ApplicationId" android:value="@string/bnc_app_key" />
</application>
```


### Starting a Branch session -- required for all SDK calls

Branch must be started within your app before any calls can be made to the SDK. Modify the following methods:

##### Objective-C

Find AppDelegate.m in the left sidebar, and edit it. Add:

```objc
#import <Branch/Branch.h>
```

Find didFinishLaunchingWithOptions and paste the following Branch code below it: 
```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
	// anything else you need to do in this method
	// ...

	Branch *branch = [Branch getInstance];
	[branch initSessionWithLaunchOptions:launchOptions andRegisterDeepLinkHandler:^(NSDictionary *params, NSError *error) {		// previously initUserSessionWithCallback:withLaunchOptions:
        if (!error) {
			// params are the deep linked params associated with the link that the user clicked -> was re-directed to this app
			// params will be empty if no data found
			// ... insert custom logic here ...   
        }
	}];
}
```

Find - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication... and paste the following at the top of the function to handle the deeplink URL:

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

#### Android

Navigate to your app's launch activity (the first one that opens up). Add the following code to the activity class:

```java
@Override
public void onStart() {
    super.onStart();

    Branch branch = Branch.getInstance(getApplicationContext());
    branch.initUserSession(new BranchReferralInitListener() {
        @Override
        public void onInitFinished(JSONObject referringParams) {
                // params are the deep linked params associated with the link that the user clicked before showing up
                Log.i("BranchConfigTest", "deep link data: " + referringParams.toString());
        }
    }, this.getIntent().getData());
}

@Override
public void onStop() {
    super.onStop();
    Branch.getInstance(getApplicationContext()).closeSession();
}
```

The deep link handler is called every single time the app is opened, returning deep link data if the user tapped on a link that led to this app open.

This same code also triggers the recording of an event with Branch. If this is the first time a user has opened the app, an "install" event is registered. Every subsequent time the user opens the app, it will trigger an "open" event.

### Routing based on the link (optional)

One great use case for Branch is showing different view controllers and content based on what link the user just clicked on. Users that download the app after clicking on a Smart Banner are often meant to be taken directly to some content that they were viewing on the mobile web. The following implementation of _application:didFinishLaunchingWithOptions:_ looks at the params dictionary that is passed in and decides which view controller to present. If the user clicked on a Branch link or Smart Banner with the parameter _pictureURL_ attached, the application redirects to a screen to view the picture. Otherwise the default view controller is shown. Obviously routing logic is heavily implementation-specific, so the code below is just an example. 

#### iOS

```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    // Initalize Branch and register the deep link handler
    // The deep link handler is called on every install/open to tell you if the user had just clicked a deep link
    Branch *branch = [Branch getInstance];
    [branch initSessionWithLaunchOptions:launchOptions isReferrable:@YES andRegisterDeepLinkHandler:^(NSDictionary *params, NSError *error) {     // previously initUserSessionWithCallback:withLaunchOptions:
        UINavigationController *navController = (UINavigationController *)self.window.rootViewController;
        NSString * storyboardName = @"Main";
        UIStoryboard *storyboard = [UIStoryboard storyboardWithName:storyboardName bundle: nil];
        UIViewController *nextVC;
        
        // If the key 'pictureURL' is present in the deep link dictionary
        // then load the picture screen with the appropriate URL
        if ([params objectForKey:@"pictureURL"]) {
            [MyAppPreferences setNextPictureURL:[params objectForKey:@"pictureURL"]];
            [MyAppPreferences setNextPictureCaption:[params objectForKey:@"pictureCaption"]];

            // Choose the picture viewer as the next VC
            nextVC = [storyboard instantiateViewControllerWithIdentifier:@"PictureViewerViewController"];
        // Else, the app is being opened up from the home screen or from the app store
        // Load the next logical view controller
        } else {
            nextVC = [storyboard instantiateViewControllerWithIdentifier:@"MainViewController"];
        }
        
        // launch the next view controller
        [navController setViewControllers:@[nextVC] animated:YES];
    }];
    
    return YES;
}
```

#### Android

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
    }, this.getIntent().getData());
}
```

##### Close session

Required: this call will clear the deep link parameters when the app is closed, so they can be refreshed after a new link is clicked or the app is reopened.

```java
@Override
public void onStop() {
    super.onStop();
    Branch.getInstance(getApplicationContext()).closeSession();
}
```

##### Retrieve session (install or open) parameters

These session parameters will be available at any point later on with this command. If no params, the dictionary will be empty. This refreshes with every new session (app installs AND app opens)
```java
Branch branch = Branch.getInstance(getApplicationContext());
JSONObject sessionParams = branch.getLatestReferringParams();
```

##### Retrieve install (install only) parameters

If you ever want to access the original session params (the parameters passed in for the first install event only), you can use this line. This is useful if you only want to reward users who newly installed the app from a referral link or something.
```java
Branch branch = Branch.getInstance(getApplicationContext());
JSONObject installParams = branch.getFirstReferringParams();
```


## 3. Implementing the Web SDK and Smart Banner

### Javascript Snippet

The Branch Web SDK provides an easy way to interact with the Branch API on your website or web app. It requires no frameworks, and is only ~7K gzipped.

Place this code before the `</head>` statement in your HTML.  Be sure to replace `APP-KEY` with your Branch app ID found in your [Branch dashboard](https://dashboard.branch.io/#/settings).

```html
<script type="text/javascript">
(function(b,r,a,n,c,h,_,s,d,k){if(!b[n]||!b[n]._q){for(;s<_.length;)c(h,_[s++]);d=r.createElement(a);d.async=1;d.src="https://cdn.branch.io/branch-v1.5.0.min.js";k=r.getElementsByTagName(a)[0];k.parentNode.insertBefore(d,k);b[n]=h}})(window,document,"script","branch",function(b,r){b[r]=function(){b._q.push([r,arguments])}},{_q:[],_v:1},"init data setIdentity logout track link sendSMS referrals credits redeem banner closeBanner".split(" "),0);

branch.init('BRANCH KEY', function(err, data) {
   // callback to handle err or data
});
</script>
```


### App Banner

This displays a Smart Banner that directs the user from the (mobile) web to the app store and passes the embedded parameter into the install so you can route the user post-install to the appropriate place (e.g. the app version of what they were viewing on the web). The banner method takes two arguments, the first being an object literal of options to set the title, description, icon, and button text of the banner, along with options for what devices to show the banner. The `data` param is the exact same as in `Branch.link()` in the Web SDK docs: [https://github.com/BranchMetrics/Web-SDK](https://github.com/BranchMetrics/Web-SDK#linklinkdata-callback)

##### Usage

```
branch.banner(
    options, // Banner options: See example for all available options
    linkData // Data for link, same as Branch.link()
);
```

**Note: this `branch.banner()` goes inside the `branch.init()` callback! See the comment in the `init()` example above.**

##### Example

```js
branch.banner({
    icon: 'http://icons.iconarchive.com/icons/wineass/ios7-redesign/512/Appstore-icon.png',
    title: 'Branch Demo App',
    description: 'The Branch demo app!',
    openAppButtonText: 'Open',         // Text to show on button if the user has the app installed
    downloadAppButtonText: 'Download', // Text to show on button if the user does not have the app installed
    sendLinkText: 'Send Link',         // Text to show on desktop button to allow users to text themselves the app
    phonePreviewText: '+44 9999-9999', // The default phone placeholder is a US format number, localize the placeholder number with a custom placeholder with this option
    iframe: true,                      // Show banner in an iframe, recomended to isolate Branch banner CSS
    showiOS: true,                     // Should the banner be shown on iOS devices?
    showAndroid: true,                 // Should the banner be shown on Android devices?
    showDesktop: true,                 // Should the banner be shown on desktop devices?
    disableHide: false,                // Should the user have the ability to hide the banner? (show's X on left side)
    forgetHide: false,                 // Should we remember or forget whether the user hid the banner?
    position: 'top',                   // Sets the position of the banner, options are: 'top' or 'bottom', and the default is 'top'
    make_new_link: false               // Should the banner create a new link, even if a link already exists?
}, {
    phone: '9999999999',
    tags: ['tag1', 'tag2'],
    feature: 'dashboard',
    stage: 'new user',
    type: 1,
    data: {
        mydata: 'something',
        foo: 'bar',
        '$desktop_url': 'http://myappwebsite.com',
        '$ios_url': 'http://myappwebsite.com/ios',
        '$ipad_url': 'http://myappwebsite.com/ipad',
        '$android_url': 'http://myappwebsite.com/android',
        '$og_app_id': '12345',
        '$og_title': 'My App',
        '$og_description': 'My app\'s description.',
        '$og_image_url': 'http://myappwebsite.com/image.png'
    }
});
```



That's all! Welcome to Branch. Hopefully that Smart Banner guide will help you convert mobile web users to app installs. Please don't hesitate to reach out with questions, comments or suggestions.

_Again, a more exhaustive guide to link creation is available at [Branch URL Creation Guide](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/url-creation-guide.md). And as always, feel free to reach out with questions._

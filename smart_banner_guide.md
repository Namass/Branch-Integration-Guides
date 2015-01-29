Smart Banner Guide for iOS
=====================================

This guide will help you integrate the smart banner for converting mobile web users to app installs while maintaining a seamless user experience. 

## 1. Create an account on the Branch Dashboard

To get started, create an account in [https://dashboard.branch.io/](https://dashboard.branch.io/). 

Navigate to the Settings page and fill in information in each of the fields, including your app store information, URI scheme, and company info. 


## 2. Setting up Branch in your app

### Installing the SDK

The Branch iOS SDK can be found at [https://github.com/BranchMetrics/Branch-iOS-SDK](https://github.com/BranchMetrics/Branch-iOS-SDK).

The Brand Android SDK can be found at [https://github.com/BranchMetrics/Branch-Android-SDK](https://github.com/BranchMetrics/Branch-Android-SDK).

#### -or- Download the raw files

Download iOS code from here:
[https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-SDK.zip](https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-SDK.zip)

The iOS testbed project:
[https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-TestBed.zip](https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-TestBed.zip)

Download JAR file from here: [https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-SDK.zip](https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-SDK.zip)

The Android testbed project: [https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-TestBed.zip](https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-TestBed.zip)


#### Importing the iOS SDK

Drag and drop the Branch.framework file that you downloaded into your project. Be sure that "Copy items if needed" is selected.

![Import SDK Demo](https://s3-us-west-1.amazonaws.com/branch-guides/8_drag.gif)

**You also need to import CoreTelephony.** See the graphic below:

![Import SDK Demo](https://s3-us-west-1.amazonaws.com/branch-guides/telephony.gif)


#### Importing the Android SDK

Open your app in your Android IDE (Eclipse or IntelliJ). Import the Branch SDK as a library project (in Eclipse, File -> Import -> Navigate to and select Branch SDK folder). 

Add the Branch library project to your project (in Eclipse, right click on your project -> Properties -> Android tab -> Add -> Select Branch SDK)



### Add your app key to your project

After you register your app, your app key can be retrieved on the [Settings](https://dashboard.branch.io/#/settings) page of the dashboard. 

For iOS, you need to add it to YourProject-Info.plist (Info.plist for Swift). Create a key called bnc_app_key, with string value: **yourAppID**

For Android, add your Branch API Key to the **res/values/strings.xml** file in your project

```java
<resources>
    <!-- Other existing resources -->

    <!-- Add this string resource below -->
    <string name="bnc_app_key">87216441102696573</string>
</resources>
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

Update your **AndroidManifest.xml** file

```java
<application>
<!-- Other existing entries -->

<!-- Add this meta-data below -->
<meta-data android:name="io.branch.sdk.ApplicationId" android:value="@string/bnc_app_key" />
</application>
```

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

One great use case for Branch is showing different view controllers and content based on what link the user just clicked on. The following implementation of _application:didFinishLaunchingWithOptions:_ looks at the params dictionary that is passed in and decides which view controller to present. If the user clicked on a Branch link with the parameter _pictureURL_ attached, the application redirects to a screen to view the picture. Otherwise the default view controller is shown. Obviously routing logic is heavily implementation-specific, so the code below is just an example. 

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


## 3. Creating custom links for the user to share

_A full guide on creating links is available: [Branch URL Creation Guide](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/url-creation-guide.md)._

Part of the beauty of Branch is that you can craft a custom user experience that leverages your existing app's architecture. One of the primary methods of creating the experience involves shareable URLs. Another method covered here is using our Smart Banner, which acts as a link behind which you can attach any custom information you'd like. This custom information is then available to other users who click the link and install the app. 


### Custom Redirects

You can do custom redirection by inserting the following _optional keys in the params dictionary_. 

| Key | Value
| --- | ---
| "$desktop_url" | Where to send the user on a desktop or laptop. By default it is the Branch-hosted text-me service
| "$android_url" | The replacement URL for the Play Store to send the user if they don't have the app. _Only necessary if you want a mobile web splash_
| "$ios_url" | The replacement URL for the App Store to send the user if they don't have the app. _Only necessary if you want a mobile web splash_
| "$ipad_url" | Same as above but for iPad Store
| "$fire_url" | Same as above but for Amazon Fire Store
| "$blackberry_url" | Same as above but for Blackberry Store
| "$windows_phone_url" | Same as above but for Windows Store
| "$deeplink_path" | The value of the deep link path that you'd like us to append to your URI. For example, you could specify "$deeplink_path": "radio/station/456" and we'll open the app with the URI "yourapp://radio/station/456?link_click_id=branch-identifier". This is primarily for supporting legacy deep linking infrastructure. 


### Features and Channels

Branch allows you to track features and channels. The more you track, the more analytics you have at your disposal. The [Dashboard's Summary tab](https://dashboard.branch.io/#) allows you to break down referrals by Feature, Channel, and a range of other metrics.

*Features*: This is a String. This is the feature of the customer’s product that the link might be associated with. For example, if someone installs from the Smart Banner, they might have tagged all those links with the String ‘smartBanner’.

*Channel*: This is a String. We recommend using channel to tag the route that your link reaches users. For example, tag links with ‘News’ or ‘Facebook’ to help track clicks and installs through those paths separately.



### Implementing the Web SDK and Smart Banner

This SDK requires native browser Javascript and has been tested in all modern browsers with sessionStorage capability. 

Place this code in the `</head>` statement in your HTML.  Be sure to replace `YOUR_APP_ID` with your Branch app ID.

```html
<script type="text/javascript">
(function() {

var config = {
app_id: 'YOUR_APP_ID',
debug: true,
init_callback: function(){
console.log('Branch SDK initialized!');
}
};

// Begin Branch SDK //
var Branch_Init=function(e){var t=this;t.app_id=e.app_id,t.debug=e.debug,t.init_callback=e.init_callback,t.queued=[],t.init=function(){for(var e=["close","logout","track","identify","createLink","showReferrals","showCredits","redeemCredits","appBanner"],n=0;n<e.length;n++)t[e[n]]=function(e){return function(){t.queued.push([e].concat(Array.prototype.slice.call(arguments,0)))}}(e[n])},t.init();var n=document.createElement("script");n.type="text/javascript",n.async=!0,n.src="https://bnc.lt/_r",document.getElementsByTagName("head")[0].appendChild(n),t._r=function(){if(void 0!==window.browser_fingerprint_id){var e=document.createElement("script");e.type="text/javascript",e.async=!0,e.src="http://s3-us-west-1.amazonaws.com/branch-web-sdk/branch-0.2.x.min.js",document.getElementsByTagName("head")[0].appendChild(e)}else window.setTimeout(function(){t._r()},100)},t._r()};window.branch=new Branch_Init(config);
// End Branch SDK //

})();
</script>
```

#### .createLink()

Create a deep linking URL.  The `data` parameter can include Facebook Open Graph data.  To read more about 
Open Graph, visit https://developers.facebook.com/docs/opengraph.

##### Usage

```
Branch.createLink(
(JSON Object, required) {
tags (JSON array, optional),           // An array of tags for splitting out data in the dashboard.
channel (string, optional),            // A string to indicate the external channel (text_message, mail, facebook, etc).
feature (string, optional),            // The string for a particular feature (invite, referral).
stage (string, optional),              // A string representing the progress of the user.
type (int, optional),                  // Use 1 for one time use links, 0 for persistent.
data (JSON object, optional) {         // This parameter takes any JSON object and attaches it to the link created.  Reserved
// parameters are denoted with '$'.
'$desktop_url' (url, optional),      // Custom redirect URL for desktop link clicks.
'$ios_url' (url, optional)           // Custom redirect URL for iOS device clicks.
'$ipad_url' (url, optional)          // Custom redirect URL for iPad clicks.  Overrides $ios_url on iPads.
'$android_url' (url, optional)       // Custom redirect URL for Android device clicks.
'$og_app_id' (string, optional)      // Facebook app ID for Open Graph data.
'$og_title', (string, optional)      // Open Graph page title.
'$og_description' (string, optional) // Open Graph page description.
'$og_image_url' (url, optional)      // Open graph page image/icon URL.
},
callback (function, optional)
}
)
```

##### Example

```js
branch.createLink({
tags: ['tag1', 'tag2'],
channel: 'facebook',
feature: 'dashboard',
stage: 'new user',
type: 1,
data: {
mydata: {
foo: 'bar'
},
'$desktop_url': 'http://myappwebsite.com',
'$ios_url': 'http://myappwebsite.com/ios',
'$ipad_url': 'http://myappwebsite.com/ipad',
'$android_url': 'http://myappwebsite.com/android',
'$og_app_id': '12345',
'$og_title': 'My App',
'$og_description': 'My app\'s description.',
'$og_image_url': 'http://myappwebsite.com/image.png'
}
}, function(data){
console.log(data)
});
```


### Smart Banners

Generate on mobile and desktop browsers to direct to app installs through deeplinking.

#### .appBanner()

Display a smart banner directing a user to your app through a Branch referral link.  The `data` param is the exact same as in `Branch.createLink()`.

##### Usage

```
Branch.appBanner(
(JSON object, required) {
icon (string, recommended),       // URL path to your app's icon.  Recommended size is 50px by 50px with no border or whitespace.
title (string, recommended)       // The title or name of your app.
description (string, recommended) // The description of your app.
data (JSON object, optional)      // Verbatim data used in Branch.createLink().  This data is passed with your clicked links.
}
)
```

##### Example

```js
branch.appBanner({
icon: 'http://icons.iconarchive.com/icons/wineass/ios7-redesign/512/Appstore-icon.png',
title: 'My App',
description: 'This is my app!',
data: {
foo: 'bar'
}
});
```



That's all! Welcome to Branch. Hopefully that Smart Banner guide will help you convert mobile web users to app installs. Please don't hesitate to reach out with questions, comments or suggestions.

_Again, a more exhaustive guide to link creation is available at [Branch URL Creation Guide](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/url-creation-guide.md). And as always, feel free to reach out with questions._

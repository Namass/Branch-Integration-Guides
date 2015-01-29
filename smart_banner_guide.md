Smart Banner Guide for iOS
=====================================

This guide will help you integrate the smart banner for converting mobile web users to app installs while maintaining a seamless user experience. 

## 1. Create an account on the Branch Dashboard

To get started, create an account in [https://dashboard.branch.io/](https://dashboard.branch.io/). 

Navigate to the Settings page and fill in information in each of the fields, including your app store information, URI scheme, and company info. 


## 2. Setting up Branch in your app

### Installing the SDK

There are multiple ways to install the Branch SDK. 

#### Available in CocoaPods

Branch is available through [CocoaPods](http://cocoapods.org), to install it simply add the following line to your Podfile:

    pod "Branch"

#### -or- Clone the Project

The Branch iOS SDK can be found at [https://github.com/BranchMetrics/Branch-iOS-SDK](https://github.com/BranchMetrics/Branch-iOS-SDK). To clone the repo from the command line:

	git clone https://github.com/BranchMetrics/Branch-iOS-SDK

The Brand Android SDK can be found at [https://github.com/BranchMetrics/Branch-Android-SDK](https://github.com/BranchMetrics/Branch-Android-SDK).

#### -or- Download the raw files

Download iOS code from here:
[https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-SDK.zip](https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-SDK.zip)

The iOS testbed project:
[https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-TestBed.zip](https://s3-us-west-1.amazonaws.com/branchhost/Branch-iOS-TestBed.zip)

Download JAR file from here: [https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-SDK.zip](https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-SDK.zip)

The Android testbed project: [https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-TestBed.zip](https://s3-us-west-1.amazonaws.com/branchhost/Branch-Android-TestBed.zip)


#### Importing the SDK

If you are using Cocoapods you should skip this step.

You will need to drag and drop the Branch.framework file that you downloaded into your project. Be sure that "Copy items if needed" is selected.

![Import SDK Demo](https://s3-us-west-1.amazonaws.com/branch-guides/8_drag.gif)

**You also need to import CoreTelephony.** See the graphic below:

![Import SDK Demo](https://s3-us-west-1.amazonaws.com/branch-guides/telephony.gif)

### Add your app key to your project

After you register your app, your app key can be retrieved on the [Settings](https://dashboard.branch.io/#/settings) page of the dashboard. Now you need to add it to YourProject-Info.plist (Info.plist for Swift).

1. In plist file, mouse hover "Information Property List" which is the root item under the Key column.
1. After about half a second, you will see a "+" sign appear. Click it.
1. In the newly added row, fill in "bnc_app_key" for its key, leave type as String, and enter your app key obtained in above steps in its value column.
1. Save the plist file.

##### Screenshot
![Setting Key in PList Demo](https://s3-us-west-1.amazonaws.com/branch-guides/10_plist.png)

##### Animated Gif
![Setting Key in PList Demo](https://s3-us-west-1.amazonaws.com/branch-guides/9_plist.gif)

Branch must be started within your app before any calls can be made to the SDK. Modify the following two methods in your App Delegate:

### Starting a Branch session -- required for all SDK calls

##### Objective-C

Find AppDelegate.m in the left sidebar, and edit it:

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

The deep link handler is called every single time the app is opened, returning deep link data if the user tapped on a link that led to this app open.

This same code also triggers the recording of an event with Branch. If this is the first time a user has opened the app, an "install" event is registered. Every subsequent time the user opens the app, it will trigger an "open" event.

##### Routing based on the link (optional)

One great use case for Branch is showing different view controllers and content based on what link the user just clicked on. The following implementation of _application:didFinishLaunchingWithOptions:_ looks at the params dictionary that is passed in and decides which view controller to present. If the user clicked on a Branch link with the parameter _pictureURL_ attached, the application redirects to a screen to view the picture. Otherwise the default view controller is shown. Obviously routing logic is heavily implementation-specific, so the code below is just an example. 

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

## 3. Creating custom links for the user to share

_A full guide on creating links is available: [Branch URL Creation Guide](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/url-creation-guide.md). The information in this section presents you with a quick way to get link creation off the ground._

Part of the beauty of Branch is that you can craft a custom user experience that leverages your existing app's architecture. One of the primary methods of creating the experience involves shareable URLs. You the developer can attach custom information to any link you create. This custom information is then available to other users who click the link.


### Custom Redirects

You have the ability to control the direct deep linking of each link by inserting the following _optional keys in the params dictionary_:

| Key | Value
| --- | ---
| "$deeplink_path" | The value of the deep link path that you'd like us to append to your URI. For example, you could specify "$deeplink_path": "radio/station/456" and we'll open the app with the URI "yourapp://radio/station/456?link_click_id=branch-identifier". This is primarily for supporting legacy deep linking infrastructure. 
| "$always_deeplink" | true or false. (default is not to deep link first) This key can be specified to have our linking service force try to open the app, even if we're not sure the user has the app installed. If the app is not installed, we fall back to the respective app store or $platform_url key. By default, we only open the app if we've seen a user initiate a session in your app from a Branch link (has been cookied and deep linked by Branch)

Also, you do custom redirection by inserting the following _optional keys in the params dictionary_. 

| Key | Value
| --- | ---
| "$desktop_url" | Where to send the user on a desktop or laptop. By default it is the Branch-hosted text-me service
| "$android_url" | The replacement URL for the Play Store to send the user if they don't have the app. _Only necessary if you want a mobile web splash_
| "$ios_url" | The replacement URL for the App Store to send the user if they don't have the app. _Only necessary if you want a mobile web splash_
| "$ipad_url" | Same as above but for iPad Store
| "$fire_url" | Same as above but for Amazon Fire Store
| "$blackberry_url" | Same as above but for Blackberry Store
| "$windows_phone_url" | Same as above but for Windows Store


### Features and Channels

Branch allows you to track features and channels. The more you track, the more analytics you have at your disposal. The [Dashboard's Summary tab](https://dashboard.branch.io/#) allows you to break down referrals by Feature, Channel, and a range of other metrics.

*Features*: This is a String. This is the feature of the customer’s product that the link might be associated with. For example, if the custom had built a referral program in their app, they might have tagged all links with the String ‘referral’.

*Channel*: This is a String. We recommend using channel to tag the route that your link reaches users. For example, tag links with ‘Facebook’ or ‘LinkedIn’ to help track clicks and installs through those paths separately.

An example illustrates another _getShortURL.._ call with Channel and Feature specified.

```objc
NSMutableDictionary *params = [NSMutableDictionary dictionary];
params[@"referringUsername"] = @"Bob";
params[@"referringUserId"] = @"1234";
    
[[Branch getInstance] getShortURLWithParams:params andChannel:@"Facebook" andFeature:@"Referral" andCallback:^(NSString *url, NSError *error) {
   // Now we can do something with the URL...
   NSLog(@"url: %@", url);
}];
```

### Creating links in the API, web SDK, and Dashboard



That's all! Welcome to Branch. Hopefully that Quick Start guide gave you some ideas. Please don't hesitate to reach out with questions, comments or suggestions.

_Again, a more exhaustive guide to link creation is available at [Branch URL Creation Guide](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/url-creation-guide.md). And as always, feel free to reach out with questions._

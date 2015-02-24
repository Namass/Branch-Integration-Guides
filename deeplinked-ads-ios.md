Deeplinked Ads
==================

This guide will help you integrate content sharing with deeplinking into your app. We will also cover several customizations you can perform. The sections are as follows:

1. [Dashboard: App Setup](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/deeplinked-ads-ios.md#1-dashboard-app-setup)
1. [Dashboard: Marketing Links](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/deeplinked-ads-ios.md#2-dashboard-marketing-links)
1. [Configuring Your App to Track Installs](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/deeplinked-ads-ios.md#3-configuring-your-app-to-track-installs-optional-but-highly-recommended)
1. [Example: Facebook Ads](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/deeplinked-ads-ios.md#4-example-facebook-ads)
1. [Personalizing A User's First Experience](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/deeplinked-ads-ios.md#5-personalizing-a-users-first-experience-advanced)
1. [Custom Events and Funnels](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/deeplinked-ads-ios.md#6-custom-events-and-funnels-advanced)


## 1. Dashboard: App Setup

To get started, create an account in [https://dashboard.branch.io/](https://dashboard.branch.io/). 

Navigate to the Settings page and fill in information in each of the fields, which at the very minimum includes:

1. App Name
1. App Store / Play Store information
1. URI Scheme

If you've already done this, you can skip ahead to section 2, Dashboard: Marketing Links. Otherwise follow the instructions below.

#### 1. App Name

To get started, point your browser to [https://dashboard.branch.io/](https://dashboard.branch.io/). If you haven't created an account before, you can signup and get taken through the basic setup right away. If you've signed up already, simply navigate to the [Summary](https://dashboard.branch.io/#) page and click the dropdown button in the top right. Choose "Create new app."

![Dashboard Screenshot](https://s3-us-west-1.amazonaws.com/branch-guides/2_dashboard.png)

You will be prompted to enter a name for your new app. Do so and press "Create."

![Dashboard Screenshot](https://s3-us-west-1.amazonaws.com/branch-guides/3_create_new_app.png)

#### 2. App Store / Play Store information

Navigate to the Settings page. Scroll down to App Store Information and search for your app by name--this is the same name listed on iTunesConnect. With this information, Branch will automatically redirect users without your app installed on their devices to the App Store.

In the case that your app cannot be found on the App Store (e.g. if you are distributing an enterprise app over the Internet, or you're not listed in the US app stores), you can also enter a custom URL by choosing "Custom URL to TestFlight/Other Host"

![Dashboard Screenshot](https://s3-us-west-1.amazonaws.com/branch-guides/4_settings_app_store.png)

#### 3. URI Scheme

On the [Settings](https://dashboard.branch.io/#/settings) page, scroll down to URI Schemes (advanced), click to expand, and add in the unique string you've chosen for your app (e.g. yourapp://). Be sure to press "Save" when you're finished.

![Dashboard Screenshot](https://s3-us-west-1.amazonaws.com/branch-guides/6_dashboard_uri.png)


## 2. Dashboard: Marketing Links

#### Creating a link

When you create links on the dashboard, you have a subset of these overall labels available to you. Visit dashboard.branch.io and select the Marketing tab. Then click **+ Add link** to get started.

The minimum required information is a Link Description. Creating links is actually this simple!

![Marketing Screen](https://s3-us-west-1.amazonaws.com/branch-guides/bpp001marketingScreen.png)

The following sections will walk through various options and their implications.

One example description if you want to treat this guide is: "Facebook ad for blue sneakers - summer 2015" -- type that in the box.

![Description](http://derrrick.com/branch/deeplinked-ads/1.png) 

#### Tags: Channel, Campaign, Etc. (highly recommended)

The more information you enter, the better you'll be able to segment data on the Dashboard and in exports. We recommend setting Channel and Campaign. Tags allows you to easily add tags that don't need to be categorized. For information of the form *[key]*: *[value]* such as "product": "shoes", we recommend adding them to "Deep Link Data (Advanced)", discussed below.

*Hint: Press the 'comma' or 'return' key after each tag.*

![Description](http://derrrick.com/branch/deeplinked-ads/2.png) 


#### Custom Link Label (optional)

This is more important for links that will be visible. For ads, this URL will be buried in the add and most users won't see it. However, you still have the option to customize it.

#### Custom Redirects (optional)

This allows you to direct the user to somewhere other than the App Store or Play Store if he does not have the app installed. This gives you flexibility as a marketer. You may decide for certain links that if they do not have the app, the user should be sent to www.yourwebsite.com/a_particular_campaign. 

Note that these redirects only come into play if the user does *not* have the app installed.


#### Social Media (OG) tags (optional but highly recommended)

While our [URL Creation Guide](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/url-creation-guide.md#dashboard-customizations) details how to set the default social media tags that will be used app-wide, you can customize these on a per-link basis as well.

For more information on the optimal format for the title and description, see Facebook's [Sharing Best Practices](https://developers.facebook.com/docs/sharing/best-practices#tags).

![Dashboard Screenshot](https://s3-us-west-1.amazonaws.com/branch-guides/5_og.png)


#### Deep Link Data (Advanced)

This is a great place to specify any other information that you think will be of use either in-app or in when combing through data later on. If you are treating this guide like a tutorial, go ahead and enter key "ad_id" and value "abc123".

![Description](http://derrrick.com/branch/deeplinked-ads/3.png) 


#### Save!

Press the greeen "Save" button when you are done editing the link.


## 3. Configuring Your App to Track Installs (optional but highly recommended)

Based on what you've built so far, you will be able to track your ad campaign's clicks by device -- how many people on each platform (desktop, iOS, Android) have clicked your ads.

If you integrate our SDK into your app, you can:

1. See exactly how many people are installing your app based on each of your ads.
2. Personalize a user's experience after clicking on an ad. This works regardless of whether the user previously had your app installed. Examples include:
	1. display a campaign-specific message
	2. automatically take the user to the same pair of shoes that were featured in the ad that was clicked on
	3. apply a certain coupon towards a purchase of a new pair of shoes, with a coupon icon at the top of the screen
3. Track events and create funnels so you can see which ads are performing best on concrete measures such as # of completed signups or number/type of purchases *even if* the app was not previously installed.


#### A. Integrating your app key

For iOS, you need to add it to YourProject-Info.plist (Info.plist for Swift). Create a key called bnc_app_key, with string value: **yourAppID**. Here are [instructions with graphics](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-quickstart.md#add-your-app-key-to-your-project).

#### B. Setting the URI scheme

URI scheme is also set in the plist, and you may already have one. Make sure it matches the URI scheme you entered on the Dashboard. If this is your first time setting a URI scheme, see these [instructions with graphics]()

#### C. Importing the SDK

Branch is available through [CocoaPods](http://cocoapods.org), to install it simply add the following line to your Podfile:

    pod "Branch"

For alternative methods including installing from a framework, [click here](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-quickstart.md#installing-the-sdk).

#### D. InitSession and OpenURL

Add the following to your AppDelegate.m file. For Swift, or more in-depth instructions, [click here](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-quickstart.md#starting-a-branch-session----required-for-all-sdk-calls).

##### Objective-C

```objc
#import <Branch/Branch.h>
```

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

#### Success!

You can now see exactly how many people are installing your app based on each of your ads! 

We are now going to take a break from code to offer an example of how to place your ad on Facebook (Section 4).

To personalize a user's first experience after tapping the ad, skip down to section 5, Personalizing A User's First Experience (Advanced).

Skip ahead to section 6, Custom Events and Funnels (Advanced), if you want to track events and create funnels. Again, this allows you to see which ads are performing best on concrete measures such as # of completed signups or number/type of purchases.



## 4. Example: Facebook Ads

Navigate to [https://www.facebook.com/ads/create](https://www.facebook.com/ads/create) while logged in to your Facebook page. (If you don't yet have a Facebook page, you will need to create one before advertising on Facebook.)

Choose "Send people to your website". Unfortunately due to a bug with Facebook's API, you cannot currently run campaigns for app downloads through the option "Get installs of your app."

![Description](http://derrrick.com/branch/deeplinked-ads/4.png) 

On the next page, you need to enter the Branch link that was generated in the last step. Here's a gif to help:

![Description](http://derrrick.com/branch/deeplinked-ads/5.gif) 

You can now customize your ad per the usual Facebook ad creation interface.

Notice that any OG tag information your provided has prepopulated in the interface.

![Description](http://derrrick.com/branch/deeplinked-ads/6.png) 

Now make sure you have a picture, then order up that ad! 

## 5. Personalizing A User's First Experience (Advanced)

The following example gives one example for customizing a user's first experience. Because it is highly app-dependent, we only provide the Branch code and a bit of example code to get a developer on her way.

The following implementation can tell if a user wanted to view a picture - even if the user just installed the app and this is the first open! If the user clicked on a Branch link with the parameter ad_id attached, the application redirects to a screen that shows a custom message based on the ad. This screen could encourage the user to "Purchase these blue sneakers now to get 20% off!" Otherwise the default view controller is shown. Obviously routing logic is heavily implementation-specific, so the code below is just an example (this example uses Storyboards). See our iOS sample project [Branchster](https://github.com/BranchMetrics/Branchster-iOS) for another example of routing.

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
        
        // If the key 'ad_id' is present in the deep link dictionary
        // then load the a screen corresponding to the ad
        NSString *adId = [params objectForKey:@"ad_id"];
        if (adId) {
            [MyAppPreferences setAdViewed: adId];

            // Choose the ad follow-up view controller as the next VC
            nextVC = [storyboard instantiateViewControllerWithIdentifier:@"AdFollowUpViewController"];
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

What you do with ad_id is highly specific to your app, but we already brainstormed a few use cases:

1. display a campaign-specific message
2. automatically take the user to the same pair of shoes that were featured in the ad that was clicked on
3. apply a certain coupon towards a purchase of a new pair of shoes, with a coupon icon at the top of the screen

Feel free to shoot us anything you think up!

## 6. Custom Events and Funnels (Advanced)

You can track custom user actions such as viewing the shoes, adding to cart and making a purchase. This will allow you to track the effectiveness of various ads (or no ads) in leading to purchases.

To track events, simply add a single line of code:

```objc
[[Branch getInstance] userCompletedAction:@"clicked ad"];
```

You can also include state, such as the ad_id from the link!

```objc
NSMutableDictionary *params = [NSMutableDictionary dictionary];
if (adId) params[@"ad_id"] = adId;
[[Branch getInstance] userCompletedAction:@"clicked ad" withState:params];
```

You can also track purchases or any other event in the same way:

```objc
NSMutableDictionary *params = @{@"product_id": @"2976"};
[[Branch getInstance] userCompletedAction:@"purchase" withState:params];
```

If you track these two events, you can now create a funnel on the Dashboard. At the bottom of the Summary page, add a funnel and choose the following events:

![Description](http://derrrick.com/branch/deeplinked-ads/7.png) 

If you locate the above lines of code next to the actual ad click and purchase events, you should now have an accurate funnel showing the conversion from ad clicks to actual purchases.

![Description](http://derrrick.com/branch/deeplinked-ads/8.png) 

That's all you need for custom events and funnels.

If you still have questions about how to implement deeplinked-ads, please send a note to support@branchmetrics.io.
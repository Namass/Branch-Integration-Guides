App Content Share with Deeplink for iOS
=======================================

This guide will help you integrate content sharing with deeplinking into your app. We will also cover several customizations you can perform. The sections are as follows:

1. [Dashboard Configuration](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-app-content-share-with-deeplink.md#1-dashboard-configuration)
1. [Configuring Your App](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-app-content-share-with-deeplink.md#2-configuring-your-app)
1. [Sharing Content](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-app-content-share-with-deeplink.md#3-sharing-content)
1. [Routing to Content](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-app-content-share-with-deeplink.md#4-routing-to-content)
1. [Identify Your Influential Users](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-app-content-share-with-deeplink.md#5-identify-your-influntial-users)
1. [Analytics](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-app-content-share-with-deeplink.md#6-analytics)
1. [Advanced Sharing and Routing](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-app-content-share-with-deeplink.md#7-advanced-sharing-and-routing-optional)


## 1. Dashboard Configuration

To get started, create an account in [https://dashboard.branch.io/](https://dashboard.branch.io/). 

Navigate to the Settings page and fill in information in each of the fields, which at the very minimum includes:

1. App Name
1. Your App Store / Play Store information
1. URI Scheme

If this is your very first time, see these [instructions with graphics](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/ios-quickstart.md#1-setting-up-your-app-on-the-branch-dashboard).


## 2. Configuring Your App

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


## 3. Sharing Content

We will start with the most basic implementation of sharing. Let's say the content you want to share is a picture with a caption. We recommend always setting the feature and channel.

```objc
NSDictionary *params = @{@"referringUsername": @"Mario",
                         @"referringUserId": @"1234",
                         @"pictureId": @"987666",
                         @"pictureURL": @"http://yoursite.com/pics/987666",
                         @"pictureCaption": @"The princess and the plumber"};
NSString *channel = @"sms"; // or @"facebook" or @"twitter", etc.
NSString *feature = BRANCH_FEATURE_TAG_SHARE;
[[Branch getInstance] getShortURLWithParams:params andChannel:channel andFeature:feature andCallback:^(NSString *url, NSError *error) {
    if (!error) {
        // url is available for sharing!
        NSLog(@"url is: %@", url);
    }
}];
```

If your app already has sharing functionality, simply replace an existing link to this piece of content with this link. Otherwise we provide multiple examples in this guide, so keep reading.


#### Optional: Social Media (OG) tags

The example below has additional tags that customize the appearance of your links on social media. 

```objc
NSDictionary *params = @{@"referringUsername": @"Mario",
                         @"referringUserId": @"1234",
                         @"pictureId": @"987666",
                         @"pictureURL": @"http://yoursite.com/pics/987666",
                         @"pictureCaption": @"The princess and the plumber",
                         @"$og_image_url": @"http://yoursite.com/pics/987666",
                         @"$og_title": @"Mario's Recent Picture",
                         @"$og_description": @"The princess and the plumber"};
NSString *channel = @"Facebook";
NSString *feature = BRANCH_FEATURE_TAG_SHARE;
[[Branch getInstance] getShortURLWithParams:params andChannel:channel andFeature:feature andCallback:^(NSString *url, NSError *error) {
    if (!error) {
        // url is available for sharing!
        NSLog(@"url is: %@", url);
    }
}];
```

Now any social media sites that make use of titles, descriptions or images will display a rich preview of your app's content to users.

A **full list** of these tags can be found [here](https://github.com/BranchMetrics/Branch-iOS-SDK/blob/master/README.md#generate-tracked-deep-linking-urls-pass-data-across-install-and-open).


#### Optional: Using MFMessageComposeViewController to share links

If your app does not already include sharing links, add the following code to the view controller where sharing should take place. Note that this includes the above URL-generation code embedded in the example.

At the top of your view controller's implementation (.m) file, include the following:

```objc
#import <MessageUI/MessageUI.h>
#import <MessageUI/MFMailComposeViewController.h>
```

Then be sure to indicate that your view controller conforms to MFMessageComposeViewControllerDelegate protocol. This is done by modifying the @interface line of your view controller's implementation (.m) file.

```objc
@interface MyAppViewController () <MFMessageComposeViewControllerDelegate>
```

The following code should go in some method triggered by the user (such as when the user taps on a button).

```objc
NSDictionary *params = @{@"referringUsername": @"Mario",
                         @"referringUserId": @"1234",
                         @"pictureId": @"987666",
                         @"pictureURL": @"http://yoursite.com/pics/987666",
                         @"pictureCaption": @"The princess and the plumber",
                         @"$og_image_url": @"http://yoursite.com/pics/987666",
                         @"$og_title": @"Mario's Recent Picture",
                         @"$og_description": @"The princess and the plumber"};
NSString *channel = @"sms";
NSString *feature = BRANCH_FEATURE_TAG_SHARE;
[[Branch getInstance] getShortURLWithParams:params andChannel:channel andFeature:feature andCallback:^(NSString *url, NSError *error) {
	if (!error) {
	    // Check to make sure we can send messages on this device
	    if ([MFMessageComposeViewController canSendText]) {
	        MFMessageComposeViewController *messageComposer = [[MFMessageComposeViewController alloc] init];
	        
	        // Set the contents of the SMS/iMessage -- be sure to include the URL!
	        [messageComposer setBody:[NSString stringWithFormat:@"You should definitely take a look at MyApp -- use my invite code to get free brownie points: %@", url]];
	        
	        messageComposer.messageComposeDelegate = self;
	        [self presentViewController:messageComposer animated:YES completion:^{
	            // ... insert code to stop the spinner here (be sure to do so on the main thread) ...

	        }];
	    } else {
	        // ... insert code to stop the spinner here (be sure to do so on the main thread) ...
	        [[[UIAlertView alloc] initWithTitle:@"Sorry" message:@"Your device does not allow sending SMS or iMessages." delegate:nil cancelButtonTitle:@"Okay" otherButtonTitles:nil] show];
	    }
    }
}];
```

Lastly, there is a required delegate method for the MessageComposeViewController. We provide an empty implementation, which you are free to customize.

```objc
- (void)messageComposeViewController:(MFMessageComposeViewController *)controller
                 didFinishWithResult:(MessageComposeResult)result {
    [self dismissViewControllerAnimated:YES completion:nil];
}
```


## 4. Routing to Content

Branch is beautiful because it allows deeplinking directly to content -- even if the user clicking the link does not have the app installed! Upon opening the app, a user can be directed straight to content and even an individually-personalized experience. 

The following implementation can tell if a user wanted to view a picture - even if the user just installed the app and this is the first open! If the user clicked on a Branch link with the parameter _pictureURL_ attached, the application redirects to a screen to view the picture. In addition, this user can be shown a personalized message, such as the following: "Thanks for checking out our app. Let's view the picture that Mario just shared with you." Otherwise the default view controller is shown. Obviously routing logic is heavily implementation-specific, so the code below is just an example (this example uses Storyboards). See our iOS sample project [Branchster](https://github.com/BranchMetrics/Branchster-iOS) for another example of routing.

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
        NSString *pictureURL = [params objectForKey:@"pictureURL"];
        if (pictureURL) {
            [MyAppPreferences setNextPictureURL:pictureURL];
            [MyAppPreferences setNextPictureCaption:[params objectForKey:@"pictureCaption"]];
            [MyAppPreferences setNextPictureReferringUserName:[params objectForKey:@"referringUsername"]];

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


## 5. Identify Your Influntial Users

#### Two Lines of Code

Branch automatically tracks unique devices. However, to make full use of our powerful API, you should also identify users with form of unique identification your app uses. The usefulness of this is hard to understate. When making future queries, when scanning through data on the dashboard, and when combing through anything you choose to export, you'll see your app's uniqueId alongside the Branch-provided ID.

The good news is that your app only needs the addition of two lines of code

Add a `setIdentity:` call wherever you create or login a user.

```objc
[[Branch getInstance] setIdentity:@"1234"]; // your app's userId
```

Add a `logout` call anywhere you allow the user to logout.

```objc
[[Branch getInstance] logout];
```

#### One Powerful Dashboard

As a side effect of what you've set up so far, you can easily see which users are your top influencers. If you navigate to the Dashboard's Referrals page and click on the [Influencers tab](https://dashboard.branch.io/#/referrals/influencers), you'll see who is referring the most users. Their Branch-determined Id is shown, along with any identification that you set during the setIdentity: call discussed above. The app-specific User ID is displayed on the left.

![Influencers](https://s3-us-west-1.amazonaws.com/branch-guides/influencers.png)

This tab is considerably more useful if you set an identity per the line of code above. Being able to use your app's actual user ID's rather than Branch-generated ones is vital for joining with your app's other data sets.


## 6. Analytics

Branch empowers advanced analytics, including tracking custom events and creating funnels.

#### Tracking Events

To track any event that you would like to use in funnels and other analytics, simply call one line of code:

```objc
[[Branch getInstance] userCompletedAction:@"sent SMS"];
```

You can modify this to contain additional state, such as:

```objc
[[Branch getInstance] userCompletedAction:@"sent SMS" withState:@{@"completedTutorial": @YES}];
```

The above could be used later to distinguish between users sharing a link before versus after working through a tutorial. 

To track when a user actually sends a link via text message, modify the following method from the code example in section 3 above:

```objc
- (void)messageComposeViewController:(MFMessageComposeViewController *)controller
                 didFinishWithResult:(MessageComposeResult)result {
    if (result == MessageComposeResultSent) {
        [[Branch getInstance] userCompletedAction:@"sent SMS"];
    }
    [self dismissViewControllerAnimated:YES completion:nil];
}
```

#### Funnels

To set up funnels on the Dashboard, at the bottom of the [Summary](http://dashboard.branch.io/#) page, choose "+ Add your first conversion funnel" and give it a name, such as "SMS shares after open". Now, choose "open" as the first event and "sent SMS" as the second event. That's it!

This is a rather trivial example since we only have one custom event. Better use cases involve tracking users through a multi-stage signup process, or from completing one custom event to completing a second. 

Another great custom event to track would be when the user enters the app to view a specific picture, as discussed in section 4 on routing. You could add this single line, which should appear immediately after the comment below:

```objc
// Choose the picture viewer as the next VC
[branch userCompletedAction:@"viewed pic" withState:@{@"pictureURL": pictureURL}];
```

#### If you want it, you can track it

This only scratches the tip of the iceberg as far as analytics is concerned--it's just enough to get you started. 

Log into the Dashboard and play around on the various tabs. If anything is unclear, shoot us an email at support@branchmetrics.io.


## 7. Advanced Sharing and Routing (optional)

### Branch custom subclass of UIActivityItemProvider

UIActivityView is the standard way of allowing users to share content from your app. A common use case is a user sharing a referral code, or a content URL with their friends. If you want to give your users a way of sharing content from your app, this is the simpelist way to implement Branch.

Sample UIActivityView Share sheet:

![UIActivityView Share Sheet](https://s3-us-west-1.amazonaws.com/branchhost/iOSShareSheet.png )

The Branch iOS SDK includes a subclassed UIActivityItemProvider that can be passed into a UIActivityViewController, that will generate a Branch short URL and automatically tag it with the channel the user selects (Facebook, Twitter, etc.). The sample app included with the Branch iOS SDK shows a sample of this in ViewController.m:

```objc
// Setup up the content you want to share, and the Branch
// params and properties, as you would for any branch link
    
// No need to set the channel, that is done automatically based
// on the share activity the user selects
NSString *shareString = @"Super amazing thing I want to share!";
NSString *defaultURL = @"http://lmgtfy.com/?q=branch+metrics";
    
NSMutableDictionary *params = [[NSMutableDictionary alloc] init];

[params setObject:@"Joe" forKey:@"user"];
[params setObject:@"https://s3-us-west-1.amazonaws.com/myapp/joes_pic.jpg" forKey:@"profile_pic"];
[params setObject:@"Joe likes long walks on the beach..." forKey:@"description"];
   
// Customize the display of the link
[params setObject:@"Joe's My App Referral" forKey:@"$og_title"];
[params setObject:@"https://s3-us-west-1.amazonaws.com/myapp/joes_pic.jpg" forKey:@"$og_image_url"];
[params setObject:@"Join Joe in My App - it's awesome" forKey:@"$og_description"];

// Customize the redirect performance
[params setObject:@"http://myapp.com/desktop_splash" forKey:@"$desktop_url"];


NSArray *tags = @[@"tag1", @"tag2"];
NSString *feature = @"invite";
NSString *stage = @"2";
    
// Branch UIActivityItemProvider
UIActivityItemProvider *itemProvider = [Branch getBranchActivityItemWithDefaultURL:defaultURL andParams:params andFeature:feature andStage:stage andTags:tags];
    
// Pass this in the NSArray of ActivityItems when initializing a UIActivityViewController
UIActivityViewController *shareViewController = [[UIActivityViewController alloc] initWithActivityItems:@[shareString, itemProvider] applicationActivities:nil];
    
// Present the share sheet!
[self.navigationController presentViewController:shareViewController animated:YES completion:nil];
```


### Jumping Ahead in the View Hierarchy

If your app has a view hierarchy that extends beyond two levels of UIViewControllers within a UINavigationController (as many apps do), you may be wondering how to jump ahead multiple view controllers.

For example, if you have a chat conversation view that is the third view controller pushed onto the view controller hierarchy, and a user taps a link to jump straight into a conversation, you may need to construct a view hierarchy as follows:

Home View Controller -> All Conversations View Controller -> One Conversation View Controller with the conversation pointed to by the URL.

If this is the case, your order of operations is:

1. instantiate all three view controllers
2. put the view controllers in an array in order (base view controller first, top (currently in view) view controller last)
3. set this array of view controllers as the navigation controller's view controllers

In code (for storyboards, assumes you have set View Controllers' Storyboard ID):

```objc
// 1.
UIStoryboard *storyboard = [UIStoryboard storyboardWithName:@"Main" bundle:nil];
HomeViewController *vc1 = [storyboard instantiateViewControllerWithIdentifier:@"HomeViewController"];
AllConversationsViewController *vc2 = [storyboard instantiateViewControllerWithIdentifier:@"AllConversationsViewController"];
OneConversationViewController *vc3 = [storyboard instantiateViewControllerWithIdentifier:@"OneConversationViewController"];

// 2.
NSMutableArray *controllers = [NSMutableArray array];
[controllers addObject:vc1];
[controllers addObject:vc2];
[controllers addObject:vc3];

// 3.
[yourAppNavigationController setViewControllers:controllers animated:YES];
```


## Feedback

Please let us know what other advanced topics you would like to see and we will add them to this guide. For suggestions or questions, please email support@branchmetrics.io.
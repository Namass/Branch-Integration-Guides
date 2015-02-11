Branch URL Creation Guide
=========================

With each Branch link, we pack in as much functionality and measurement as possible. You get the powerful deep linking functionality in addition to the all the install and reengagement attribution, all in one link.

It is possible to _white-label_ the domain of the links with your own subdomain or domain. For example, you can use yoursite.com/l/13xJfm--73 instead of bnc.lt/l/13xJfm--73. The instructions and configuration can be found in the Settings page on the Branch dashboard. Linked [here](http://dashboard.branchmetrics.io/#/settings).

# Link Types

### Short Links (Require async call)

Short links look like so: https://bnc.lt/l/13xJfm--73 and can be generated via:

1. The [iOS SDK](https://github.com/BranchMetrics/Branch-iOS-SDK#generate-tracked-deep-linking-urls-pass-data-across-install-and-open) and [Android SDK](https://github.com/BranchMetrics/Branch-Android-SDK#generate-tracked-deep-linking-urls-pass-data-across-install-and-open)
1. The [public API](https://github.com/BranchMetrics/Branch-Public-API#creating-a-deeplinking-url)
1. The [web SDK](https://github.com/BranchMetrics/Web-SDK#createlink)
1. The [dashboard](https://dashboard.branchmetrics.io/#/marketing)

### Long Links (Construct via formatting)

Long links don't require an asynchronous call and be created by structuring the data and analytics tags you need into the URL string itself. [Here](https://github.com/BranchMetrics/Branch-Public-API#structuring-a-dynamic-deeplink) is the document that describes how to structure it.

Below is an example of one:
https://bnc.lt/a/5680621892404085?feature=download&data=eyJjaGFubmVsIjoiIiwiY2FtcGFpZ24iOiIiLCIkaW9zX3VybCI6Imh0dHBzOi8vaXR1bmVzLmFwcGxlLmNvbS91cy9hcHAvZXZlbnRib3gvaWQ5MTM3MDcyMDE%2FbHM9MSZtdD04IiwiJGlwYWRfdXJsIjoiaHR0cHM6Ly9pdHVuZXMuYXBwbGUuY29tL3VzL2FwcC9ldmVudGJveC9pZDkxMzcwNzIwMT9scz0xJm10PTgiLCIkZGVza3RvcF91cmwiOiJodHRwczovL2l0dW5lcy5hcHBsZS5jb20vdXMvYXBwL2V2ZW50Ym94L2lkOTEzNzA3MjAxP2xzPTEmbXQ9OCIsImZlYXR1cmUiOiJkb3dubG9hZCJ9

# Dynamic Link Creation Examples

## The Android or iOS SDK ([Android](https://github.com/BranchMetrics/Branch-Android-SDK#generate-tracked-deep-linking-urls-pass-data-across-install-and-open) or [iOS](https://github.com/BranchMetrics/Branch-iOS-SDK#generate-tracked-deep-linking-urls-pass-data-across-install-and-open))

Here is an example URL creation call in iOS. This would be called after using the initSession call with the appropriate app key to register the native library for your app.

```objc
NSMutableDictionary *params = [[NSMutableDictionary alloc] init];

[params setObject:@"Joe" forKey:@"user"];
[params setObject:@"https://s3-us-west-1.amazonaws.com/myapp/joes_pic.jpg" forKey:@"profile_pic"];
[params setObject:@"Joe likes long walks on the beach..." forKey:@"description"];

Branch *branch = [Branch getInstance:@"Your app key"];
[branch getShortURLWithParams:params andChannel:@"sms" andFeature:BRANCH_FEATURE_TAG_SHARE andStage:@"added_to_cart" andCallback:^(NSString *url, NSError *error) {
	if(!error) {
		// embed the link into an SMS for sharing
	}
}];
```

## The public API ([found here](https://github.com/BranchMetrics/Branch-Public-API))

Here is an example CURL call to create a link with some example parameters. You would specify the app_id key with the application key you received for your customer in the original POST to /v1/app. The data field is the dictionary of stuff that you want to appear in the app after a user installs or opens from this link, also known as the deep link parameters. The remainder of the tags are all optional and explained in great detail below.

	curl -X POST 

	-H "Content-Type: application/json" 

	-d '{"app_id":"5680621892404085", 
	"campaign":"announcement", 
	"feature":"invite", 
	"channel":"email", 
	"tags":["4"], 
	"data":"{\"name\":\"Alex\", 
		\"email\":\"alex@branch.io\", 
		\"$desktop_url\":\"https://branch.io\"}"
	}' 

	https://api.branch.io/v1/url

This will return a dictionary like so, with your specific link.

	{
   		'url’ : ‘https://bnc.lt/ADaEf23-0’
	}

## The dashboard ([found here](http://dashboard.branch.io))

When you create links on the dashboard, you have a subset of these overall labels available to you. Visit dashboard.branch.io and select the Marketing tab. Then click Add link to get started.

![Marketing Screen](https://s3-us-west-1.amazonaws.com/branch-guides/bpp001marketingScreen.png)

## The web SDK

to be inserted

## The mobile web smart banner

to be inserted

# Link Analytics

Whether you create links via the dashboard or the API, you have a bunch of ways to organize the links for tracking. There are fields for the following labels that be applied to the link, and help you organize your outreach efforts and the onslaught of data. All of these custom labels will be available to you when a user installs the app. 

All of these tags and labels are optional.

## Dashboard link analytics options

This first set is what you have access to when you use the dashboard.

	channel		// This is a free form entry of any types of characters.
				// We recommend using channel to tag the route that your 
				// link reaches users. For example, tag links with ‘Facebook’ 
				// or ‘LinkedIn’ to help track clicks and installs through those
				// paths separately.

	campaign	// This is a free form entry with any types of characters.
				// We recommend using this field to organize the links by actual 
				// campaign. Let’s say you launched a new feature or product
				// and want to run a campaign around that, you would organize 
				// all your links around this

	tags		// This accepts an unlimited number of strings.
				// This is more a free form entry of anything that 
				// helps you collect your thoughts. It’s a place where 
				// you can add meaningful labels for organizing links
				// that aren’t confined to a channel or campaign

## API link analytics expanded options

If you decide to employ the public API for link creation, you have all of the labels above, plus the expanded feature set listed below

	identity 	// This is a free form entry of any type of characters.
				// This is the identity of a customer’s user that the link 
				// might be associated with. It will be in the form of the 
				// customer’s own internal user id, and should be the same as 
				// what’s used for setIdentity call of the SDK itself. If you 
				// don’t want the links to be associated with a user 
				// (for the purpose of tracking referrals and influencers), 
				// then just leave this key out

	feature 	// This is a free form entry of any type of character.
				// This is the feature of the customer’s product that the 
				// link might be associated with. For example, if the custom 
				// had built a referral program in their app, they might have 
				// tagged all links with the String ‘referral’.

	stage		// This is a free form entry of any type of characters
				// This is a label typically used to categorize the progress
				// or category of a user when the link was generated. For example, 
				// if the customer has an invite system accessible on level 1, level 3 and 5, 
				// the customer could differentiate links generated at each level with this parameter

	type 		// This is an integer value of 0 or 1.
				// Specify this value if the link is to be used once and
				// discarded. For security reasons, a customer might want to 
				// specify that the link is only deep linkable a single time. 
				// The example would be if user login credentials were embedded
				// in the link, they might want to expire the link after it’s 
				// been used the first time

# Customized link display

A customer might want links generated that appear as a premium post like the example below, when posted to Facebook or Twitter. In this case, you’ll need to specify the open graph (OG) tags for each link, so that Facebook knows to display the content properly.

![Premium Post](https://s3-us-west-1.amazonaws.com/branch-guides/bpp002premiumPost.jpg)

Currently, we have 3 options to customize the appearance of the link: title, description and image.  

## Dashboard customizations

If you create links in the dashboard, you’ll find the interface to customize the appearance of the link under the collapsable tab ‘Social Media Description’ as seen in the screenshots below.

![Marketing Link](https://s3-us-west-1.amazonaws.com/branch-guides/bpp003marketingLink.png)

When you expand it, you’ll find the ability to customize all of the fields listed above, as shown in this screenshot. In short, you can customize the 

![Marketing Link Creative](https://s3-us-west-1.amazonaws.com/branch-guides/bpp004marketingLinkCreative.png)

## API customizations

If you are creating links via the API, the way to properly configure the appearance of the link is by specifying a few keys in the ‘data’ dictionary of the URL creation POST. 
In the example below, the title, description and image were specified by employing the custom keys $og_title, $og_description and $og_image_url. 

	curl -X POST 

	-H "Content-Type: application/json" 

	-d '{"app_id":"5680621892404085", 
	"campaign":"announcement", 
	"feature":"invite", 
	"channel":"email", 
	"tags":["4"], 
	"data":"{\"name\":\"Alex\", 
		\"email\":\"alex@branch.io\", 
		\"$og_title\":\"My First Branch Link\", 
		\"$og_description\":\"This is how to customize link appearances\", 
		\"$og_image_url\":\"https://branch.io/img/logo.png\"}
	"}' 

	https://api.branch.io/v1/url

Here is the specification:

	$og_title	// This is the title that will appear in the top of the rich post, as seen in the example above
	$og_description	// This is the description that will sit below the title, as seen in the example above
	$og_image_url	// This is the image that will be loaded into the rich post, as seen in the example above
	$og_video	// If you have a link to a video, use this tag
	$og_url		// This is the URL you want to appear in the OG summary
	
If you don't want us to host the OG tags, and want us to host the OG tags on another piece of real estate, you can use the custom key $__og_redirect. This tells us service where to send the scrapers for Facebook, Twitter and Pinterest, and allows you to control the full extent of OG tags.

	$__og_redirect	// This is the key to use in order to override our hosting of OG tags with your own page

# Custom link redirects

We offload a lot of the complexity of building links for your apps, as we allow you to completely customize the redirects depending on the operating system of the user. We have a complex set of logic that governs the redirection, depending on whether the app is installed and whether the link redirects have been overridden. See the diagram below for the full details.

![Marketing How Links Work](https://s3-us-west-1.amazonaws.com/branch-guides/bpp005textMeFeature.jpg)

## Dashboard customization

If you are creating links via the dashboard, it’s possible to manually customize the endpoints for each operating system. First open up the link creator or editor as seen in the screenshot below.

![Marketing Link](https://s3-us-west-1.amazonaws.com/branch-guides/bpp006marketingLink.png)

Then choose “Custom Redirects” in the tab, to see the section below.

![Marketing Link Redirects](https://s3-us-west-1.amazonaws.com/branch-guides/bpp007customRedirects.png)

In this section, you can use the default settings or customize each endpoint easily by platform. Choose the custom radio button and paste in the URL that you want to use as the redirect endpoint for each platform, or select the default to use that destination.

By default, we send on Android and iOS, we send them to the respective app store that is specified, or the web URL specified in settings if no app store destination is configured. 

On the desktop, the default setting is a Branch hosted text-me-the-app page which allows users to send themselves a link to download the app from their phone. Here’s a sample screenshot of that page, which will be customized with the details specified about the app.

![Marketing Text Me](https://s3-us-west-1.amazonaws.com/branch-guides/bpp008textMeFeature.png)

## API customizations

If you are creating links via the API, the way to properly configure the redirects of the link is by specifying a few keys in the ‘data’ dictionary of the URL creation POST. 

Below is an example where I have customize the **$desktop_url** and **$ios_url** to point to ‘https://branch.io’ and ‘https://branch.io/ios_site’. This will override the default endpoints for each platform, which are the Branch-hosted text-me-the-app page for desktop and the App Store for iOS. Anyone clicking on this link from an Android phone, will be taken to the Google Play store by default. Of course, all of this is overridden if Branch knows that the user has the app and the mobile app has configured their URI scheme

	curl -X POST 

	-H "Content-Type: application/json" 

	-d '{"app_id":"5680621892404085", 
	"campaign":"announcement", 
	"feature":"invite", 
	"channel":"email", 
	"tags":["4"], 
	"data":"{\"name\":\"Alex\", 
	\"email\":\"alex@branch.io\", \"$desktop_url\":\"https://branch.io\", \"$ios_url\":\"https://branch.io/ios_site\"}"}' https://api.branch.io/v1/url

The possible platform redirects are the following:
	$desktop_url	// This key will override the default desktop destination 
					// with your home page. 
					// A common example of this use case would be if the 
					// customer wanted all links to point to their homepage. 
					// By default, if this key is not specified, we take the 
					// user to this custom text-me-the-app page.

![Marketing Text Me](https://s3-us-west-1.amazonaws.com/branch-guides/bpp008textMeFeature.png)

	$ios_url 
	$android_url
	$ipad_url
	$windows_phone_url
	$blackberry_url
	$fire_url		// These keys will override the redirect location of 
					// the link when clicked on these various endpoints. 
					// The default is the app store page if specified, 
					// and the web url (in app settings) if no app is specified.
					// Warning: Currently, Android Chrome does not support 
					// the $android_url override.

We recently enabled the ability for you to control whether you link tries to open the app by default or not.

	"$always_deeplink"	// true or false. (default is not to deep link first) 
						// This key can be specified to have our linking service
						// force try to open the app, even if we're not sure 
						// the user has the app installed. If the app is not 
						// installed, we fall back to the respective app store 
						// or $platform_url key. By default, we only open the app 
						// if we've seen a user initiate a session in your app 
						// from a Branch link (has been cookied and deep linked by Branch)

It's easy to support legacy deep link paths, you simply need to specify the key:

	"$deeplink_path"	// With this key, use value of the deep link path that 
						// you'd like us to append to your URI. For example, 
						// you could specify "$deeplink_path": "radio/station/456"
						//  and we'll open the app with the URI 
						// "yourapp://radio/station/456?link_click_id=branch-identifier". 
						// This is primarily for supporting legacy deep linking infrastructure. 

# Deep linked data

Every single link can be packed with a custom dictionary of data that will be delivered inside the app after install, or to the web destination if the customer is using the web SDK. This package of data will help the app deeplink to a page in the app, or customize the post install experience. Additionally, if you registered for the web hook, this custom set of parameters will be posted back to your server when an install/open or any custom event is referred from a link.

This deep link data can be specified in either the dashboard or the API.

## Dashboard customization

The dashboard has an option to specify the custom deep link data in the section in the last drop down section. First, open up the edit or create link menu in the Marketing page to see this.

![Marketing Link](https://s3-us-west-1.amazonaws.com/branch-guides/bpp009marketingLink.png)

Choose Deep Link Data to see the interface below.

![Marketing Link Deep Link Params](https://s3-us-west-1.amazonaws.com/branch-guides/bpp010marketingDeepLinkParams.png)

You can add in any amount of custom keys and values that will be passed to the app through install or open. In the screenshot above, if you had wired up the app to show the courseId embedded in the link, anyone who clicks the link and ultimately installs the app will be passed to course ID 415123.

## API customization

If you are creating links via the API, the it’s very simple to customize the package of data to be sent to the app post install.

Below is an example where I embed the keys ‘name’, ‘email’ and ‘courseId’ with values of ‘Alex’, ‘alex@branch.io’ and ‘415’123’ into the link. These parameters will be accessible inside the app if anyone clicks the link then initializes a new session.

	curl -X POST 

	-H "Content-Type: application/json" 

	-d '{"app_id":"5680621892404085", 
	"campaign":"announcement", 
	"feature":"invite", 
	"channel":"email", 
	"tags":["4"], 
	"data":"{\"name\":\"Alex\", 
		\"email\":\"alex@branch.io\", 
		\"courseId\":\"415123\"}
	"}' 

	https://api.branch.io/v1/url

This will return a dictionary like so, with your specific link.

	{
   		'url’ : ‘https://bnc.lt/ADaEf23-0’
	}

# Link alias name

If you would like to use a custom label for your links, we have the ability of you to assign a custom string in place of our code. For example, you can use https://bnc.lt/l/special_download instead of our standard randomly generated code.

These link aliases are unique and immutable, meaning that once you've created it, you can't delete it or reassign it. They are unique to each domain, meaning you can only have one link called 'alex' link for bnc.lt and for yoursite.com. For example, once bnc.lt/alex is created by one app, no other app can use the 'alex' alias. However, if you use your own domain (yoursite.com), you can create a yoursite.com/alex.

## Dashboard customization

An alias can only be specified for a link when it is _first_ being created. It cannot be changed or removed once it has been applied. To create a link, first go to create a link by clicking the Add link button in the Marketing section seen below.

![Marketing Screen](https://s3-us-west-1.amazonaws.com/branch-guides/bpp001marketingScreen.png)

The next step would be to specify the alias you'd like to use in the field under Custom Link Label, as shown in the screenshot below.

![Marketing Alias Entry](https://s3-us-west-1.amazonaws.com/branch-guides/bpp014UrlAlias.png)

After you've chosen a name you'd like to use, you can try to save it. If you get an error, it'll be because that string is already taken. At that point, you'll need to try a new string.

## API customization

You can also dynamically create aliases through our API. To do this, all you need to do is specify the 'alias' key with the value being hte desired label for your link. Once you have created an aliased link, it can be retrieved through the exact same call with exact same parameter set. If you alter any of the additional parameters of the link and try to retrieve it again, you'll get an HTTP 409 error code.

	curl -X POST 

	-H "Content-Type: application/json" 

	-d '{"app_id":"5680621892404085",
	"alias":"AUSTIN48"
	"campaign":"announcement", 
	"feature":"invite", 
	"channel":"email", 
	"tags":["4"], 
	"data":"{\"name\":\"Alex\", 
		\"email\":\"alex@branch.io\", 
		\"$desktop_url\":\"https://branch.io\"}"
	}' 

	https://api.branch.io/v1/url

This will return a dictionary like so, with your specific link.

	{
   		'url’ : ‘https://bnc.lt/AUSTIN48’
	}

If the link was already created, and you don't specify an exact match for the remaining parameters (campaign, channel, app_id, etc..), this call will return an HTTP 409 error code.

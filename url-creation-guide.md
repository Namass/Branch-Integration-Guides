Branch URL Creation Guide
=========================

With each Branch link, we pack in as much functionality and measurement as possible. You get the powerful deep linking functionality in addition to the all the install and reengagement attribution, all in one link.

There are two methods for link creation:

#### The public API ([found here](https://github.com/BranchMetrics/Branch-Public-API))_

Here is an example CURL call to create a link with some example parameters. You would specify the app_id key with the application key you received for your customer in the original POST to /v1/app. The data field is the dictionary of stuff that you want to appear in the app after a user installs or opens from this link, also known as the deep link parameters. The remainder of the tags are all optional and explained in great detail below.

	curl -X POST 

	-H "Content-Type: application/json" 

	-d '{"app_id":"5680621892404085", 
	"campaign":"announcement", 
	"feature":"invite", 
	"channel":"email", 
	"tags":["4"], 
	"data":"{\"name\":\"Alex\", 
		\"email\":\alex@branch.io\", 
		\"$desktop_url\":\"https://branch.io\"}
	"}' 

	https://api.branch.io/v1/url

This will return a dictionary like so, with your specific link.

	{
   		'url’ : ‘https://bnc.lt/ADaEf23-0’
	}

#### The dashboard ([found here](http://dashboard.branch.io))

When you create links on the dashboard, you have a subset of these overall labels available to you. Visit dashboard.branch.io and select the Marketing tab. Then click Add link to get started.

![Marketing Screen](https://s3-us-west-1.amazonaws.com/branch-guides/bpp001marketingScreen.png)

# Link Analytics

Whether you create links via the dashboard or the API, you have a bunch of ways to organize the links for tracking. There are fields for the following labels that be applied to the link, and help you organize your outreach efforts and the onslaught of data. All of these custom labels will be available to you when a user installs the app. 

All of these tags and labels are optional.

## Dashboard Link Options

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

## API Link Options

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

## Dashboard customization

If you create links in the dashboard, you’ll find the interface to customize the appearance of the link under the collapsable tab ‘Social Media Description’ as seen in the screenshots below.

![Marketing Link](https://s3-us-west-1.amazonaws.com/branch-guides/bpp003marketingLink.png)

When you expand it, you’ll find the ability to customize all of the fields listed above, as shown in this screenshot.

![Marketing Link Creative](https://s3-us-west-1.amazonaws.com/branch-guides/bpp004marketingLinkCreative.png)

## API customization

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
		\"email\":\alex@branch.io\", 
		\"$og_title\":\"My First Branch Link\", 
		\"$og_description\":\"This is how to customize link appearances\", 
		\"$og_image_url\":\"https://branch.io/img/logo.png\"}
	"}' 

https://api.branch.io/v1/url

Here is the specification:

	$og_title		// This is the title that will appear in the top of the rich post, as seen in the example above
	$og_description	// This is the description that will sit below the title, as seen in the example above
	$og_image_url	// This is the image that will be loaded into the rich post, as seen in the example above

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

## API customization

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
	\"email\":\alex@branch.io\", \"$desktop_url\":\"https://branch.io\", \"$ios_url\":\"https://branch.io/ios_site\"}"}' https://api.branch.io/v1/url

The possible platform redirects are the following:
	$desktop_url	// This key will override the default desktop destination 
					// with your home page. 
					// A common example of this use case would be if the 
					// customer wanted all links to point to their homepage. 
					// By default, if this key is not specified, we take the 
					// user to this custom text-me-the-app page.

![Marketing Text Me](https://s3-us-west-1.amazonaws.com/branch-guides/bpp008textMeFeature.png)

	$ios_url 
	$android_url 	// These keys will override the redirect location of 
					// the link when clicked on these various endpoints. 
					// The default is the app store page if specified, 
					// and the web url (in app settings) if no app is specified.

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
		\"email\":\alex@branch.io\", 
		\"courseId\":\"415123\"}
	"}' 

	https://api.branch.io/v1/url
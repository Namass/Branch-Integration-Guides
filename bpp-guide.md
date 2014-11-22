Branch Preferred Partner Integration Guide
=========================

This guide should be used by Branch Preferred Partners who want to create Branch accounts (white-labeled or otherwise) for their existing customers. An example of this would be a Facebook PMD who wants to use the Branch links for deep linked install advertisements on behalf of their advertiser, and incorporate the attribution or conversion data into their own dashboard. Or, a developer service (eg LinkTexting.com) that wants to incorporate the Branch install links and attribution data into their own analytics or service. 

By creating a Branch account, you will have the power to create links and receive any data that Branch receives on behalf of your existing customer.

## Table of Contents:
1. One time initial registration
2. One time setup for each customer
2.1 Create a Branch account for the app
2.2 Register a web hook to receive attribution data
2.3 Have the customer integrate Branch native libraries
3. Ongoing functionality
3.1 Creating deep links
3.2 Monitoring the customer's dashboard

## One time initial registration

First, you will need a Branch partner token that gives you the ability to create/edit Branch accounts on behalf of your customers. This is a simple process where Branch reviews your application and, if approved, creates a token for you. To apply, just send us a note at http://branch.io/contact. We’ll send you an invite to join the dashboard for one of our sample apps to get a feel for the dashboard interface if you want to try some of the technology. 

The token will be in the form of a long series of numbers like this: ‘19190933253783894’. If you lose this token, just send a note to your contact at Branch or submit a request via the form. 

### Create a Branch account for them

Before you can start creating links for your customers, you must first create a Branch account for them. This account will yield a unique app key that must be used for all subsequent requests on behalf of that app. The app key format is the same format as your preferred partner token, for example: “19190933253783894”. If you lose this token for the app, you can just go through the same process to create a new version with all of the previous settings.

A Branch app specifies some of the global parameters, unique to a mobile app on the store, but can span across all app store. For example, if the customer has a few app in the App Store, you’ll want to create one Branch account per App Store app. However, if the customer has one app across the App Store and Google Play, you’ll only need one Branch account.

Branch accounts can only be created via the API, through the spec listed at this [Github page](https://github.com/BranchMetrics/Branch-Public-API#creating-a-new-branch-app-config), or as described here. The app API follows standard REST guidelines, where POST creates the app, PUT updates the fields, and GET retrieves the current parameters.

To retrieve the current configuration for the app, please use the following endpoint:

	GET
	https://api.branch.io/v1/app/app_id

Example:

	https://api.branch.io/v1/app/19190933253783894

To create (or update) a new configuration for an app, please using the following endpoint:
	
	POST (PUT)
	Content-Type: application/json
	https://api.branch.io/v1/app

Example:

	curl -X POST 

	-H "Content-Type: application/json" 

	-d '{ "user_id":"53283841485308661", 
	"app_name":"Kindred Photo Books", 
	"dev_name":"Alex", 
	"dev_email":"alex@branch.io", 
	"ios_url":"https://itunes.apple.com/us/app/kindred-photo-books/id685455736", 
	"android_url":"https://play.google.com/store/apps/details?id=com.kindred.android", 
	"web_url":"http://kindredprints.com/home"}' 

	https://api.branch.io/v1/app

Here is the specification:

	Required Parameters:
	user_id			// This is the Branch partner token that was provided to you
	app_name 		// This is the name of the mobile app that the links will be pointing to
	dev_name		// This is the name of the developer who will be the main point of contact for the customer
	dev_email		// This is the email of the developer who will be the main point of contact for the customer

	Optional Parameters:
	android_url			// This is the URL of the Google Play page
	android_uri_scheme	// This is the deep link scheme that the Android app responds to
	ios_url				// This is the URL of the App Store page
	ios_uri_scheme		// This is the deep link scheme that the iOS app responds to
	web_url				// This is the fallback URL if the App Store or Google Play pages don’t exist

	// This are the default Open Graph (OG) display tags to show when a link is pasted on Facebook or Twitter	
	og_title
	og_description
	og_image_url

### Register a web hook for all attribution data from Branch

Our web hook system allows you to receive all install and down funnel event data on behalf of the customer, for install attribution or conversion funnels in your own dashboard. You simply need to specify a URL for us to send all this data to.

The web hook system is very powerful and customizable. You can register to only receive notifications for specific events, like an ‘install’ event for example, or you can register a wildcard ‘*’ and receive all events. You can specify to only receive an event for the first time a user completes it, or every time. You can also specify receive events only in the case of referrals.

#### Webhook Syntax Specification 

Here is the format of what we post to you

	POST
	User-agent: Branch Metrics API
	Content-Type: application/json
	{
		event: ‘event name’
		metadata: ‘event metadata’ - specified in userCompletedAction withState
		hardware_id: ‘IDFA’

		// optionally included:
		identity: ‘user ID’ - specified in setIdentity
		
		// the referrer who created the new user	
		first_referring_identity: ‘user ID’ - specified in setIdentity
		first_hardware_id: ‘IDFA’
		first_referring_link_data: { link data dictionary - see below }

		// the referrer who referred this session
		session_referring_identity: ‘user ID’
		session_referring_hardware_id: ‘IDFA’
		session_referring_link_data: { link data dictionary - see below }
	}

	// link data dictionary example
	{
		data: { deep link dictionary }
		type: 0 - original, 1 - one time use, 2 - marketing
		campaign: ‘campaign label’
		feature: ‘feature label’
		channel: ‘channel label’
		tags: [tags array]
		stage: ‘stage label’
	}

#### Register Web Hook on Dashboard

To register a web hook on the dashboard, open up dashboard.branch.io and click on the ‘Referrals’ tab. The primary use case of this system is for referral rewards, but we’ve extended it to allow for web hooks as well.

Click Add a new rule to get started.

![Marketing Link Reward Rules](https://s3-us-west-1.amazonaws.com/branch-guides/bpp011rewardRulesSource.png)

_Choose event for which to receive web hook calls_

The first step is to register for which events you’d like to receive a web hook for. These can be tailored to specific, custom events, like ‘complete_purchase’ or ‘install’, or can be generalized to a wildcard event ‘*’ which will return every single event tracked through Branch.

Branch automatically tracks ‘installs’, ‘opens’, ‘referred session’ and ‘web session start’ events as soon as the customer starts using the SDK. Any other events will be recorded through the userCompletedAction function of the native library.

The example below shows a wildcard webhook with event name ‘*’. 

![Marketing Link Reward Rules Wildcard](https://s3-us-west-1.amazonaws.com/branch-guides/bpp012rewardRulesWildcard.png)

_Choose frequency of web hook_

There are two options for frequency. Either you can receive a web hook for every single event called or can receive it the first time only for each user.

_Add filters_

Filters are a set of advanced features where basically you can choose to only receive a webhook call when a matching key/value pair is found in the event metadata. Event metadata is passed via the SDK with userCompletedAction withState, where state is a dictionary of key/value pairs. For example, let’s say you only want a web hook to be called when someone signs up in Chicago. You can specify “city”:”Chicago” in the “Filters” section of the web hook. Then, in app, whenever ‘city’:’Chicago’ is passed in the state dictionary, it will trigger the web hook.

This is typically used for advanced integrations, and likely won’t be used.

_Enter your web hook URL_

Lastly, select the Web Hook toggle button and enter in your own web server URL that you would like the events to be posted to. After you save, you should see the web hook in the list of your reward rules.

![Marketing Link Reward Rules Done](https://s3-us-west-1.amazonaws.com/branch-guides/bpp013rewardRulesDone.png)

#### Register Web Hook on API

In addition to the dashboard, you have the ability to create these webhooks on behalf of your customers via the API. This allows to register what we call ‘event responses’, which are actions that we taken after an event is detected in the native or web library. 

To create web hook event response, use the following API endpoint. The full documentation is listed here in our [API documentation](https://github.com/BranchMetrics/Branch-Public-API#creating-a-remote-event-for-funnels) but reproduced below with examples for convenience.

Here is an example to create a wild card web hook, which will POST http://mywebsite.com/branch with the JSON spec at the top of this section.

	curl -X POST 

	-H "Content-Type: application/json"
	 
	-d '{"app_id":"21079185439064757", 
	"calculation_type":"0", "location":"0", "type":"web_hook", "event":"*", 
	"metadata":"{\"web_hook_url\":\"http://mywebsite.com/branch\"}"}'  
	
	https://api.branch.io/v1/eventresponse

Here is the specification:

	POST
	https://api.branch.io/v1/eventresponse
	Content-Type: application/json

	Required Parameters:
	app_id	 			// This is the app id of your customer (returned in the app create call)

	type 				// specify the value of ‘web_hook’

	location 			// specify 0 to receive web hook POSTs for the user taking action.

	calculation_type	// specify 0 to receive a POST every time the user takes action. 
						// You can specify 1 to receive a POST only the first time a user completes the events

	event  				// This is the event that you want to receive a web hook POST for. 
						// For example, “install” or “signup”. 
						// If you want to receive all events, just specify “*” for our wildcard event

	metadata  			// This is a JSON dictionary where you specify your web hook endpoint where 
						// we should post. Please use the key “web_hook_url” and the value of your URL. 
						// For example: { “web_hook_url”:”http://mywebsite.com/branch” }

	Optional Parameters:
	filter 				// This is for advanced web hook users. Specify a custom dictionary of 
						// key/value pairs that must be matched by the event metadata before the 
						// web hook is triggered. Event metadata is passed by the native library as state. 
						// The value of this key must be in JSON dictionary format.


### Have the customer integrate Branch native libraries

As soon as you register your customer’s app through our API, they will receive an email to get started. If they have any confusion, you can forward them to dashboard.branch.io, where we have a full walkthrough to drop the native library in. 

It’s a very quick process that simplifies the traditional complexities of hooking up deep linking to the app. Below is a summary of the key integration points for the customer to focus on.

#### Init session and register deep link handler

To handle the deferred deep linking, you'll just need to monitor for any referring link data in the callback for _initSessionWithLaunchOptions andRegisterDeepLinkHandler_. The customer must call this every single time the app opens up, and Branch will call the deep link handler immediately to inform whether there were any links clicked prior to the app session.

It’s best practice to add routing in this deep link handler that will load a particular page in the app depending on data that’s passed back in the handler. Branch stores these deep link parameters  to be retrieved later in the app session.  To retrieve the latest parameters, call _getLastReferringParams_. To retrieve the very first deep link params, call _getFirstReferringParams_.

#### Creating deep links in app

Our deep links can be created dynamically in the app, and embedded with any custom content. For example, it’s typical for mobile apps to use our links for sharing or invite systems, where they want the link to drive to the app and deliver custom content post install or open. Additionally, this allows the app to tie new installs back to the exact user and feature.

To create links, here are some of the typical customizations:
1. Use _getShortUrlWithParams_ in the library to retrieve a URL dynamically
2. Specify the deep link data in the ‘params’ field, with any amount of custom key value pairs:
    2.1. For example referral code for referral links OR user data for personalized installs
    2.2. **$desktop_url** to where you want the user to go on a non-mobile device (we have a JS library to handle a text-me feature if you'd like, or just send them to your home page. Our default is a hosted text-me page.
    2.3 Customize each links OG tags with **$og_title**, **$og_description**, **$og_image_url**. The defaults can be set on the dashboard in Settings
3. Specify the channel (twitter, facebook, sms, etc) to see how each referral channel is performing
4. Specify the feature as 'referral' or 'sharing' depending the type

This call will return the URL with all of the specified parameters 

#### Alias user data to a user ID (optional)

Branch ties all events and links created in app to the user of that app. This helps show which users are the most influential. If you want to see this data according to the customer’s concept of the user ID, it’s important to call setIdentity in app with the proper user ID.

#### Track events (optional)

If the customer would like to track major user events, like signup and purchase through Branch, in order to tie these back to the source, they can use userCompletedAction in the native library. These will be sent back to you via the web hook if you registered for one.

## Ongoing monitoring

### Creating deep links

With each Branch link, we pack in as much functionality and measurement as possible. You get the powerful deep linking functionality in addition to the all the install and reengagement attribution, all in one link.

There are two methods for link creation as a Branch Preferred Partner:

#### The public API ([found here](https://github.com/BranchMetrics/Branch-Public-API))

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

#### The public API ([found here](http://dashboard.branch.io))

When you create links on the dashboard, you have a subset of these overall labels available to you. Visit dashboard.branch.io and select the Marketing tab. Then click Add link to get started.

![Marketing Screen](https://s3-us-west-1.amazonaws.com/branch-guides/bpp001marketingScreen.png)

_Link Analytics_

Whether you create links via the dashboard or the API, you have a bunch of ways to organize the links for tracking. There are fields for the following labels that be applied to the link, and help you organize your outreach efforts and the onslaught of data. All of these custom labels will be available to you when a user installs the app. 

All of these tags and labels are optional.

Dashboard Link Options

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

API

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

_Customized link display_

A customer might want links generated that appear as a premium post like the example below, when posted to Facebook or Twitter. In this case, you’ll need to specify the open graph (OG) tags for each link, so that Facebook knows to display the content properly.

![Premium Post](https://s3-us-west-1.amazonaws.com/branch-guides/bpp002premiumPost.jpg)

Currently, we have 3 options to customize the appearance of the link: title, description and image.  

Dashboard

If you create links in the dashboard, you’ll find the interface to customize the appearance of the link under the collapsable tab ‘Social Media Description’ as seen in the screenshots below.

![Marketing Link](https://s3-us-west-1.amazonaws.com/branch-guides/bpp003marketingLink.png)

When you expand it, you’ll find the ability to customize all of the fields listed above, as shown in this screenshot.

![Marketing Link Creative](https://s3-us-west-1.amazonaws.com/branch-guides/bpp004marketingLinkCreative.png)

API

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

The possible OG customization keys are as follows:
	$og_title		// This is the title that will appear in the top of the rich post, as seen in the example above
	$og_description	// This is the description that will sit below the title, as seen in the example above
	$og_image_url	// This is the image that will be loaded into the rich post, as seen in the example above

_Custom link redirects_

We offload a lot of the complexity of building links for your apps, as we allow you to completely customize the redirects depending on the operating system of the user. We have a complex set of logic that governs the redirection, depending on whether the app is installed and whether the link redirects have been overridden. See the diagram below for the full details.

![Marketing How Links Work](https://s3-us-west-1.amazonaws.com/branch-guides/bpp005textMeFeature.jpg)

Dashboard

If you are creating links via the dashboard, it’s possible to manually customize the endpoints for each operating system. First open up the link creator or editor as seen in the screenshot below.

![Marketing Link](https://s3-us-west-1.amazonaws.com/branch-guides/bpp006marketingLink.png)

Then choose “Custom Redirects” in the tab, to see the section below.

![Marketing Link Redirects](https://s3-us-west-1.amazonaws.com/branch-guides/bpp007customRedirects.png)

In this section, you can use the default settings or customize each endpoint easily by platform. Choose the custom radio button and paste in the URL that you want to use as the redirect endpoint for each platform, or select the default to use that destination.

By default, we send on Android and iOS, we send them to the respective app store that is specified, or the web URL specified in settings if no app store destination is configured. 

On the desktop, the default setting is a Branch hosted text-me-the-app page which allows users to send themselves a link to download the app from their phone. Here’s a sample screenshot of that page, which will be customized with the details specified about the app.

![Marketing Text Me](https://s3-us-west-1.amazonaws.com/branch-guides/bpp008textMeFeature.png)

API

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

_Deep linked data_

Every single link can be packed with a custom dictionary of data that will be delivered inside the app after install, or to the web destination if the customer is using the web SDK. This package of data will help the app deeplink to a page in the app, or customize the post install experience. Additionally, if you registered for the web hook, this custom set of parameters will be posted back to your server when an install/open or any custom event is referred from a link.

This deep link data can be specified in either the dashboard or the API.

Dashboard

The dashboard has an option to specify the custom deep link data in the section in the last drop down section. First, open up the edit or create link menu in the Marketing page to see this.

![Marketing Link](https://s3-us-west-1.amazonaws.com/branch-guides/bpp009marketingLink.png)

Choose Deep Link Data to see the interface below.

![Marketing Link Deep Link Params](https://s3-us-west-1.amazonaws.com/branch-guides/bpp010marketingDeepLinkParams.png)

You can add in any amount of custom keys and values that will be passed to the app through install or open. In the screenshot above, if you had wired up the app to show the courseId embedded in the link, anyone who clicks the link and ultimately installs the app will be passed to course ID 415123.

API

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

#### Monitoring the customer's Branch dashboard

As a Preferred Partner, you have the ability to log into the Branch dashboard at dashboard.branch.io and survey the list of customers that you have access to. Dashboard access is tied to the original email that the preferred partner token was associated with.

If you happen to forget what the email is, just email your contact at Branch or submit a contact request with your Preferred Partner token, and we’ll get back to you in a few hours.
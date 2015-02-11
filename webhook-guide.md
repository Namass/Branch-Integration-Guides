Branch Webhook Integration Guide
=========================

Our webhook system allows you to receive all install and down funnel event data from us as it occurs, for install attribution or conversion funnels in your own database. You simply need to specify a URL for us to send all this data to.

The webhook system is very powerful and customizable. You can register to only receive notifications for specific events, like an ‘install’ event for example, or you can register a wildcard ‘*’ and receive all events. You can specify to only receive an event for the first time a user completes it, or every time. You can also specify receive events only in the case of referrals.

## Webhook Syntax Specification

Here is the format of what we post to you

	POST
	User-agent: Branch Metrics API
	Content-Type: application/json
	{
		event: ‘event name’
		metadata: ‘event metadata’ - specified in userCompletedAction withState
		hardware_id: ‘IDFA’
		os: 'iOS' | 'Android'

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

## Register Webhook on Dashboard

To register a webhook on the dashboard, open up dashboard.branch.io and click on the ‘Webhooks’ tab.

Click Add a new webhook to get started.

![Add Webhook](https://s3-us-west-1.amazonaws.com/branch-guides/webhook001_v2_addEventResponse.png)

#### Enter Your Webhook URL

We've layed out the webhook registration in a sentence format. The format is:

Send a webhook to [ WEBHOOK URL ] [ EVERY TIME / THE FIRST TIME ] users trigger the event [ EVENT ]

First, enter the webhook URL in your own web server URL that you would like the events to be posted to.

#### Choose frequency of webhook:

There are two options for frequency (As seen below). Either you can receive a webhook for every single event called or can receive it the first time only for each user.

![Webhook - Specify Event Name](https://s3-us-west-1.amazonaws.com/branch-guides/webhook003_v2_SpecifiyFrequency.png)

#### Choose event for which to receive webhook calls:

The first step is to register for which events you’d like to receive a webhook for. These can be tailored to specific, custom events, like ‘complete_purchase’ or ‘install’, or can be generalized to a wildcard event ‘*’ which will return every single event tracked through Branch.

Branch automatically tracks ‘installs’, ‘opens’, ‘referred session’ and ‘web session start’ events as soon the native library is run on a device. Any other events will be recorded through the userCompletedAction function of the native library.

The example below shows a wildcard webhook with event name ‘*’.

![Webhook - Specify Event Name](https://s3-us-west-1.amazonaws.com/branch-guides/webhook002_v2_SpecifiyEventName.png)


#### Add filters:

Filters are a set of advanced features where basically you can choose to only receive a webhook call when a matching key/value pair is found in the event metadata. Event metadata is passed via the SDK with userCompletedAction withState, where state is a dictionary of key/value pairs. For example, let’s say you only want a webhook to be called when someone signs up in Chicago. You can specify “city”:”Chicago” in the “Filters” section of the webhook. Then, in app, whenever ‘city’:’Chicago’ is passed in the state dictionary, it will trigger the webhook.

This is typically used for advanced integrations.

After you save, you should see the webhook in the list of your reward rules.

![Reward Rules Finished](https://s3-us-west-1.amazonaws.com/branch-guides/webhook004_v2_ListView.png)

## Register Webhook on API

In addition to the dashboard, you have the ability to create these webhooks via the API. This allows to register what we call ‘event responses’, which are actions that we taken after an event is detected in the native or web library.

To create webhook event response, use the following API endpoint. The full documentation is listed here in our [API documentation](https://github.com/BranchMetrics/Branch-Public-API#creating-a-remote-event-for-funnels) but reproduced below with examples for convenience.

Here is an example to create a wild card webhook, which will POST http://mywebsite.com/branch with the JSON spec at the top of this section.

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

	location 			// specify 0 to receive webhook POSTs for the user taking action.

	calculation_type	// specify 0 to receive a POST every time the user takes action.
						// You can specify 1 to receive a POST only the first time a user completes the events

	event  				// This is the event that you want to receive a webhook POST for.
						// For example, “install” or “signup”.
						// If you want to receive all events, just specify “*” for our wildcard event

	metadata  			// This is a JSON dictionary where you specify your webhook endpoint where
						// we should post. Please use the key “web_hook_url” and the value of your URL.
						// For example: { “web_hook_url”:”http://mywebsite.com/branch” }

	Optional Parameters:
	filter 				// This is for advanced webhook users. Specify a custom dictionary of
						// key/value pairs that must be matched by the event metadata before the
						// webhook is triggered. Event metadata is passed by the native library as state.
						// The value of this key must be in JSON dictionary format.


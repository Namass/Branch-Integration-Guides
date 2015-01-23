Android Quick-Start Guide
=========================
This quick start guide shows you how to integrate deep-linking functionality into an existing app project with the Branch SDK for Android.

## Step 0: Sign up for a Branch account
The first step in the process of integrating Branch with your Android app is to sign up via the dashboard. This step is largely self-explanitory, so we'll jump into what you do once you're in.

[ optionally a screenshot of the sign up process ]
<!--
![Create your account][create_your_account]
[create_your_account]: images/create_your_account_now.png =600px"Create your account."
-->

## Step 1: Register your app in the Branch Dashboard

### Getting an API Key	
API Keys are unique per-app and required by the SDK to identify your app. If you've just signed up, you're presented with a checklist of four steps that you need to carry out to configure your app.

Go to Settings on the left hand side, and use the drop-down box in the top right to create a new app.

<div style="text-align:center">
<img src="images/create_new_app_button.png" width="140" >
</div>

Enter the name of your app in the dialog then hit Create.

<div style="text-align:center">
<img src="images/dashboard_create_a_new_app.png" width="480" >
</div>


You'll be presented with the Settings page for your newly created app. Up top is your API key, which will be used in your app to identify it to the Branch servers.

<div style="text-align:center">
<img src="images/dashboard_api_key.png" width="480" >
</div>

Now open up your IDE:

- If you're using Android Studio, skip to X.
- If Eclipse is your chosen IDE, skip to Y.

<!--

## Step X: Android Studio
The easiest way to get started with Android Studio is to clone the Branchster-Android project directly from GitHub.

<div style="text-align:center;font-weight:bold;font-style:italic;">
Coming Soon
</div>

-->

## Step Y: Eclipse

Note: If you're familiar with importing projects, you can import the Branchster-Android project and Branch SDK dependency and run straight to step Z - configuring the API key.

Download or clone the Branchster-Android project to your machine, then import it into Eclipse. 

Go to:
File > Import 
[ image of the import dialog in Eclipse ]


Browse to the directory that you unzipped the project to and select it in the dialog. Hit finish to complete the import.

At this point the project will not build, and you'll see some problems relating to a missing library.

Next follow the same process for the Branch SDK; download it from GitHub, unzip it and import it into your Eclipse workspace.

o

Right-click on the Branchster-Android project and open the project properties <!-- Todo: (Mac - &#8984;I, Windows )-->

<div style="text-align:center">
<img src="images/eclipse_import_existing_code_fadeout.png" width="480" >
</div>

<div style="text-align:center">
<img src="images/eclipse_import_app_project_fadeout.png" width="800" >
</div>

<div style="text-align:center">
<img src="images/eclipse_import_sdk_project_fadeout.png" width="800" >
</div>

<div style="text-align:center">
<img src="images/eclipse_library_added.png" width="480" >
</div>

<div style="text-align:center">
<img src="images/eclipse_select_sdk_fadeout.png" width="480" >
</div>

<div style="text-align:center">
<img src="images/eclipse_select_sdk.png" width="480" >
</div>

<!--
![Create your account][require_steps_checklist]
[require_steps_checklist]: images/required_steps_checklist.png =600px"Create your account."
-->

<!--

![Step 1: Complete basic SDK setup][step_1]
[step_1]: images/step_1.png =600px "1. Complete basic SDK setup"


![Step 2: Complete basic SDK setup][step_2]
[step_2]: images/step_2.png =600px "2. Create your first link"


![Step 3: Complete basic SDK setup][step_3]
[step_3]: images/step_3.png =600px "3. Integrate SDK into your app" 

-->

## Step X1: Configuring your API key
Now you'll need the API key that you created in step GGG. 

This guide assumes that you're familiar with the [Android UI lifecycle](http://developer.android.com/training/basics/activity-lifecycle/starting.html).

A single Branch object instance is used per Activity or Fragment, so declare an object at the class-level.

```Java

	public YourClass extends Activity {
	
		Branch branch;
		...
		
	}



In the overridden *onStart* method in your Activity or Fragment, get an instance of the Branch object and assign it to the class-level handle that you just declared.

Insert your API key as the second parameter of the *getInstance()* call as shown.

```Java

	branch = Branch.getInstance(this.getApplicationContext(), "your-API-Key-goes-here");


## Step 2: Getting a session

First, get an instance of the Branch object for use throughout your Activity; you should declare this in the top-level of your class so that you can refer to it throughout.



```Java

	@Override
	protected void onStart() {
		super.onStart();

		Branch branch = Branch.getInstance(this.getApplicationContext(), "your-API-Key-goes-here");
		
		...
		
	}


The next step is to call initSession. This connects to the Branch servers in preparation for receiving the links that you're to configure, or to look up the link that your app has just received.

As this action is Asynchronous, the response is taken care of by a *BranchReferralListener()* which we need to configure first.

```Java

	Branch branchReferralInitListener = new BranchReferralInitListener() {
			@Override
			public void onInitFinished(JSONObject referringParams, BranchError error) {
			
				// Do this when a response is returned.
				...
				
			}
		}

Next, call *initSession()* as shown here to initialise the Branch session; the last two parameters pass the data associated with an incoming intent, and *this* refers to the activity context.

**Note:** If you're running this in a Fragment, use *getActivity()* instead of *this*.

```Java

	branch.initSession(branchReferralInitListener, this.getIntent().getData(), this);


## Step 3: Closing the session

In order for the SDK to know that you're finished with the Branch object, it's important to close the session when you're done.
		

```Java

	@Override
	protected void onStop() {
		super.onStop();
		
		branch.closeSession();

	}

That's all there is to it. The next step is to create some deep links for your users to share!


## Step 4: Creating deep links from within your app

[In progress]

```Java

branch.getShortUrl(tags, "channel1", "feature1", "1", obj, new BranchLinkCreateListener() {
                        @Override
                        public void onLinkCreate(String url, BranchError error) {

                            // Backwards try-catch to see if error is null...does the job, not elegant though.
                            try {
                                Log.w(TAG, "onLinkCreate() ERROR: " + error.getMessage());
                            } catch (Exception e){
                                Log.v(TAG, "onLinkCreate() URL: " + url);
                                tvShortUrl.setText(url);

                                tvShortUrl.setOnClickListener(new View.OnClickListener() {
                                    @Override
                                    public void onClick(View v) {
                                        Intent browserIntent = new Intent(Intent.ACTION_VIEW, Uri.parse(((TextView)v).getText().toString()));
                                        startActivity(browserIntent);
                                    }
                                });
                            }


                        }
                    });
                    
```
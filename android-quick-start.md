Android Quick-Start Guide
=========================
This quick start guide shows you how to get up and running fast with the Branch SDK for Android.


<p align="center"><img src="https://s3-us-west-1.amazonaws.com/branch-guides/guide_to_the_guide.png" alt="a guide to this guide"></p>


# *Step 0* - Sign Up & Setup

If you don't already have a Branch account, [head here and sign up for one](https://dashboard.branch.io/). It's free!

## The Dashboard

Our dashboard is the starting point for adding apps as well as tracking users of your app.

To get started, point your browser to https://dashboard.branch.io/. If you haven't created an account before, you can signup and get taken through the basic setup right away. If you've signed up already, simply navigate to the Summary page and click the dropdown button in the top right. Choose "Create new app."

<img align="middle" src="https://camo.githubusercontent.com/977650e449925021e8617c0ce8e4edef0831fd14/68747470733a2f2f73332d75732d776573742d312e616d617a6f6e6177732e636f6d2f6272616e63682d6775696465732f325f64617368626f6172642e706e67" alt="Sign Up To Create Your Account">

You will be prompted to enter a name for your new app. Do so and press "Create."

<img align="middle" src="https://s3-us-west-1.amazonaws.com/branch-guides/3_create_new_app.png"/>

# Step 1 - Register Your App

## Getting an API Key	
API Keys are unique per-app and required by the SDK to identify your app. If you've just signed up, you're presented with a checklist of four steps that you need to carry out to configure your app.

Go to Settings on the left hand side, and use the drop-down box in the top right to create a new app.

<p align="center">
<img align="middle" src="https://s3-us-west-1.amazonaws.com/branch-guides/create_new_app_button.png" alt="Create New App Button" width="140" >
</p>

Enter the name of your app in the dialog then hit Create.

<img align="middle" src="https://s3-us-west-1.amazonaws.com/branch-guides/dashboard_create_a_new_app.png" alt="Create A New App In The Dashboard">

You'll be presented with the Settings page for your newly created app. Up top is your API key, which will be used in your app to identify it to the Branch servers.

<p align="center"><img src="https://s3-us-west-1.amazonaws.com/branch-guides/dashboard_api_key.png"></p>

Scroll down to the *App store information* section and select your app by searching for it within the *Google Play Search* box provided. If youre app is published outside of the Play Store, you can alternatively provide a *Custom URL to APK* that points to a publically accessible location where users can download your app - this comes in handy if you haven't yet published your app and want to try out the features of Branch before you do.

<img src="https://s3-us-west-1.amazonaws.com/branch-guides/select_your_app.gif" alt="you can select your app from the Play Store if it is already published"/>

# Step 2 - Set Up Your IDE

This part depends on your personal preference:

* if you prefer **Android Studio** jump straight to *Step 3a*.
* if you are an **Eclipse** user, jump straight to *Step 3b*.

If you are new to Android development and are not familiar with either of the above, we recommend [installing *Android Studio*](http://developer.android.com/sdk/) to get started.

<!--
Now open up your IDE:

- If you're using Android Studio, skip to X.
- If Eclipse is your chosen IDE, skip to Y.
-->

<!--

## Step X: Android Studio
The easiest way to get started with Android Studio is to clone the Branchster-Android project directly from GitHub.

<div style="text-align:center;font-weight:bold;font-style:italic;">
Coming Soon
</div>

Todo - copy content from local-studio.md.

-->

# Step 3a - Android Studio

As Android Studio makes use of Gradle dependencies and the Branch SDK is hosted in Maven Central, the set up process is straightforward.

<p align="center"><img src="https://s3-us-west-1.amazonaws.com/branch-guides/android_studio_gradle_dependency.gif"></p>

* Right click on the main module within your project (this is called 'app' by default).
* Select **Open Module Settings**.
* Within the **Dependencies** tab, click the **+** button at the bottom of the window and select **Library Dependency**.
* Type *branch*, and hit the enter key to search Maven Central for the Branch SDK Library.
* Select the latest *io.branch.sdk.android:library* item listed and accept the changes.

Android Studio will then run a Gradle Sync automatically and will import the library files that are required for you.

To test that you've imported the dependencies successfully, declare a Branch object as follows.

```Java
Branch branch;
```

If there's no error highlighted in red, you're ready to **go to Step 4**.




# Step 3b - Eclipse

[Download](https://github.com/BranchMetrics/Branch-Android-SDK/archive/master.zip) or [clone](https://github.com/BranchMetrics/Branch-Android-SDK.git) the Branch SDK to your machine, and import it into your Eclipse workspace alongside the project that you want to integrate with Branch.

<p align="center"><img src="https://s3-us-west-1.amazonaws.com/branch-guides/eclipse_import_library.gif"></p>

* Go to File > Import
* Browse to the directory that you unzipped the project to and select it in the dialog.
* Check **Copy projects into workspace**, and hit finish to complete the import.

At this point the project will not build, and you'll see some problems relating to a missing library. To correct that:

* Right click on *your* project in the left hand pane and select **Properties**.
* Select *Android* from the left hand pane, and in the *Libraries* section on the right, click **Add**.
* Select Branch-SDK from the list of available library projects, and hit **ok**.

<!-- Todo: (Mac - &#8984;I, Windows )-->

Select "Android" on the left, then click the "Add" button, then select Branch-SDK to add the project to your app as as a dependency.

<img src="https://s3-us-west-1.amazonaws.com/branch-guides/eclipse_select_sdk_fadeout.png">

Once that's done, you'll see a green tick next to the "Library" list, confirming that it's been added correctly. Click "OK" to confirm.

<img src="https://s3-us-west-1.amazonaws.com/branch-guides/eclipse_library_added.png">

To test that you've imported the dependencies successfully, declare a Branch object as follows.

```Java
Branch branch;
```

If there's an error highlighted in red, hover over it and select *Import 'Branch' (io.branch.referral)*. You're now ready to **go to Step 4**.



<!--
<div style="text-align:center">
<img src="images/eclipse_library_added.png" width="480" >
</div>
-->

<!--
<div style="text-align:center">
<img src="images/eclipse_select_sdk_fadeout.png" width="480" >
</div>
-->

<!--
<div style="text-align:center">
<img src="images/eclipse_select_sdk.png" width="480" >
</div>
-->

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

# Step 4 - Create a Branch Session

## Configuring your API key
Now you'll need the API key that you created in the [Branch Dashboard](https://dashboard.branch.io/). 

Open up **strings.xml** and add your key as a String value.

```XML
<string name="bnc_app_key">your app key</string>
```

Now add a reference to the value that your just created in your **AndroidManifest.xml** file, within the Application element.

```XML

<application .. >
	
	<!-- Your existing activities, intent filters and associated attributes. -->
	<activity .. >
		..
	</activity>

	<!-- The reference to the string value that you created in strings.xml, with name as shown. -->
	<meta-data android:name="io.branch.sdk.ApplicationId" android:value="@string/bnc_app_key" />
	
</application>
```
The Branch SDK looks for the **io.branch.sdk.ApplicationId** meta-data value when it is initialised. If you want to base other projects on this one and would like to track link analytics separately in the dashboard, you need only to create a new app in the Branch dashboard and update the value in strings.xml with the new key in order to associate the project with the new project.

This guide assumes that you're familiar with the [Android UI lifecycle](http://developer.android.com/training/basics/activity-lifecycle/starting.html). A single Branch object instance is used per Activity or Fragment, so declare an object at the class-level, and you can call this in every Activity or Fragment where you need to interact with Branch; if it has already be initialised elsewhere in your app, the same instance will be returned.

```Java
public YourClass extends Activity {
	Branch branch;
	...
}
```

In the overridden *onStart* method in your Activity or Fragment, get an instance of the singleton Branch object and assign it to the class-level handle that you just declared.

```Java
@Override
protected void onStart() {
	super.onStart();

	branch = Branch.getInstance(this.getApplicationContext());
	...
}
```
**Note:** *If you have already integrated the SDK via the two-parameter getInstance(context, "app key") method, it has been deprecated. You should update your project to [store your app key as meta data in your AndroidManifest file](https://github.com/BranchMetrics/Branch-Android-SDK/blob/2cb4f05fd8f67bce1019456f26b7f384a39abb2c/README.md#add-your-app-key-to-your-project).*

## Getting a session

The next step is to call *initSession(...)*. This connects to the Branch servers in preparation for receiving the links that you're to configure, or to look up the link that your app has just received.

As this action is Asynchronous, the response is taken care of by a *BranchReferralListener()* which we need to configure first.

```Java
Branch branchReferralInitListener = new BranchReferralInitListener() {
	@Override
	public void onInitFinished(JSONObject referringParams, BranchError error) {
		
		// Do this when a response is returned.
		...
				
	}
}
```

Next, call **initSession()** as shown here to initialise the Branch session; the last two parameters pass the data associated with an incoming intent, and *this* refers to the activity context.

**Note:** If you're running this in a Fragment, use *getActivity()* instead of *this*.

```Java
branch.initSession(branchReferralInitListener, this.getIntent().getData(), this);
```

## Important: Closing the session

In order for the SDK to know that you're finished with the Branch object, it's important to close the session when you're done, so add the **closeSession()** call to your *onStop* method.
		

```Java
@Override
protected void onStop() {
	super.onStop();
	
	branch.closeSession();
}
```

That's all there is to it. The next step is to create some deep links for your users to share.


## Creating deep links from within your app

<!-- In progress -->

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

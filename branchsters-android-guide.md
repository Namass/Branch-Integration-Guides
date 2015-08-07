{% highlight %}
# This doc has been deprecated.  
Please visit [dev.branch.io](https://dev.branch.io/recipes/advanced_referral_incentives/android/) for the most up to date version of this guide and other learning resources for Branch.
{% endhighlight %}

Basic Branch Integration Guide for Android
=========================

By the end of this guide, you will have walked through the integration steps of our demo app, [Branchsters for Android](https://github.com/BranchMetrics/Branchster-Android).

The purpose of the app is to let a user create a completely custom monster by choosing the color, face, body and name. After settling on its unique features, the user then receives a Branch short link for that monster that can be shared on Facebook, Twitter, Email and SMS. When this link is clicked, if the clicking person doesn't have the app, they get taken to the Play Store. After installing the app, the new user sees the monster they had just clicked on (deep linked through the Play Store). If the clicking person has the app, the app opens immediately and the person sees the monster (regular deep link).

The demo app shows off the power of the links to point to specific content inside of an app. The links take the clicking person to the best possible experience, ensuring that they see the content (are deep linked) no matter if they have the app installed at first or not.

# Table Of Contents

| Chapter | Title | Description
| --- | --- | ---
| [1.]() | Getting started | Download the Branchsters code base and set up your environment to follow along
| [2.]() | Get your app key | Register on the Branch dashboard and get your application key
| [3.]() | Configure your dashboard | Configure all the settings associated with your app on the Branch dashboard
| [4.]() | Initialize the user session | Add in the two lines of code that allows you to start deeplinking, then configure the deep link router.
| [5.]() | Create your links for sharing | Create the deep links for the user to share with friends via email, sms, Twitter and Facebook
| [6.]() | Set the user identity | Set the alias for each user for analytics purposes in the Branch dashboard
| [7.]() | Add event tracking | Track user events for conversion funnel analysis on the Branch dashboard
| 8. | Coming soon - Configure the mobile web app smart banner | Configure the smart banner to deep link to the app from the mobile web. See the [web SDK documentation](https://github.com/BranchMetrics/Branch-Web-SDK) in the mean time

# 1. Getting started

Before you dig into the code itself, there are some configuration steps that you need to complete. Whether you prefer [Android Studio](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-quick-start.md#step-3a---android-studio) or [Eclipse](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-quick-start.md#step-3b---eclipse) there are [instructions for how to set up your IDE](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-quick-start.md#step-2---set-up-your-ide) in our [Android Quick-Start Guide](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-quick-start.md).

# 2. Get your app key

If you don't already have a Branch account, [head here and sign up for one](https://dashboard.branch.io/). It's free!

# 3. Configure your dashboard

Once you have an account, you can get your app key from the Branch Dashboard; for instructions for how to do that check out the [Android Quick-Start Guide](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-quick-start.md#step-0---sign-up--setup).

# 4. Initialize the user session


```
Branch branchReferralInitListener = new BranchReferralInitListener() {
    @Override
    public void onInitFinished(JSONObject referringParams, BranchError error) {

        // Do this when a response is returned.
        ...

    }
}
```
Next, call initSession() as shown here to initialise the Branch session; the last two parameters pass the data associated with an incoming intent, and this refers to the activity context.

Note: If you're running this in a Fragment, use getActivity() instead of this.

```
import io.branch.referral.Branch;

public class SplashActivity extends Activity {

	// This class-level Branch reference is included in every activity.
	Branch branch;
	
	

```

# 5. Create your links for sharing

##SMS
```
// Share via Twitter.
        Fabric.with(this, new TweetComposer());
        final Context context = this;
        cmdTwitter.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                Branch.getInstance(getApplicationContext()).getContentUrl("sms", prepareBranchDict(), new BranchLinkCreateListener() {
                    @Override
                    public void onLinkCreate(String url, BranchError error) {
                        TweetComposer.Builder builder = new TweetComposer.Builder(context)
                                .text("Check out my Branchster named " + monsterName + " at " + url);
                        builder.show();
                    }
                });
            }
        });
```

##Email
```
// Share via Twitter.
        Fabric.with(this, new TweetComposer());
        final Context context = this;
        cmdTwitter.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                Branch.getInstance(getApplicationContext()).getContentUrl("sms", prepareBranchDict(), new BranchLinkCreateListener() {
                    @Override
                    public void onLinkCreate(String url, BranchError error) {
                        TweetComposer.Builder builder = new TweetComposer.Builder(context)
                                .text("Check out my Branchster named " + monsterName + " at " + url);
                        builder.show();
                    }
                });
            }
        });
```

##Facebook
```
// Share via Facebook.
        uiHelper = new UiLifecycleHelper(this, null);
        uiHelper.onCreate(savedInstanceState);
        cmdFacebook.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                Branch.getInstance(getApplicationContext()).getContentUrl("sms", prepareBranchDict(), new BranchLinkCreateListener() {
                    @Override
                    public void onLinkCreate(String url, BranchError error) {
                        shareViaFacebook(url, "Check out my Branchster named " + monsterName);
                    }
                });
            }
        });
```

##Twitter

```
// Share via Twitter.
        Fabric.with(this, new TweetComposer());
        final Context context = this;
        cmdTwitter.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                Branch.getInstance(getApplicationContext()).getContentUrl("sms", prepareBranchDict(), new BranchLinkCreateListener() {
                    @Override
                    public void onLinkCreate(String url, BranchError error) {
                        TweetComposer.Builder builder = new TweetComposer.Builder(context)
                                .text("Check out my Branchster named " + monsterName + " at " + url);
                        builder.show();
                    }
                });
            }
        });
```

# 6. Set the user identity

After any point you identify a user, it's helpful to set a user identity so that we can alias our internal ID for a device to whatever you have called it. This helps with everything from your own internal reporting to identifying power users of your app. 

To identify a user, just call:
```java
Branch.getInstance(getApplicationContext()).setIdentity("your user id"); // your user id should not exceed 127 characters
```

Even without setIdentity, we will have our own internal ID assigned to all your apps' devices.


# 7. Add event tracking

Navigate to `MonsterViewerActivity.java`. Inside, you'll notice the following line of code:

```
Branch.getInstance(getApplicationContext()).userCompletedAction("monster_view", monsterMetadata);
```

`monsterMetadata` is of type `JSONObject` and contains the following values:

```
monsterMetadata.put("color_index", prefs.getColorIndex());
monsterMetadata.put("body_index", prefs.getBodyIndex());
monsterMetadata.put("face_index", prefs.getFaceIndex());
monsterMetadata.put("monster_name", prefs.getMonsterName());
monsterMetadata.put("monster", "true");
monsterMetadata.put("$og_title", "My Branchster: " + monsterName);
monsterMetadata.put("$og_description", monsterDescription);
monsterMetadata.put("$og_image_url", "https://s3-us-west-1.amazonaws.com/branchmonsterfactory/" + (short)prefs.getColorIndex() + (short)prefs.getBodyIndex() + (short)prefs.getFaceIndex() + ".png");
```

By sending this up to our dashboard, you'll be able to track events and data associated with who reached these events. You can even create custom funnels based off these events!
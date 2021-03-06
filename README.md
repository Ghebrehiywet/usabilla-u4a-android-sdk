[![Download](https://api.bintray.com/packages/usabilla/maven/ubform/images/download.svg)](https://bintray.com/usabilla/maven/ubform/_latestVersion)

# Usabilla for Apps - Android SDK
Usabilla for Apps allows you to collect feedback from your users with great ease and flexibility.

The new Usabilla SDK Version 6 comes with a new visual design with **cards**, a much cleaner interface and several bug-fixes

* * *

- [Usabilla for Apps - Android SDK](#usabilla-for-apps---android-sdk)
  - [Requirements](#requirements)
  - [Android API limitations](#android-api-limitations)
  - [Installation](#installation)
  - [Initialization](#initialization)
    - [Debug Mode](#debug-mode)
  - [Campaigns](#campaigns)
    - [The App Id](#the-app-id)
    - [Targeting options](#targeting-options)
    - [Managing an existing campaign](#managing-an-existing-campaign)
    - [Campaign results](#campaign-results)
    - [Update fragment manager](#update-fragment-manager)
    - [Reset campaigns data](#reset-campaigns-data)
  - [Passive feedback](#passive-feedback)
    - [Loading a form](#loading-a-form)
    - [Preloading a form](#preloading-a-form)
    - [Adding a screenshot](#adding-a-screenshot)
  - [Feedback submission callback](#feedback-submission-callback)
    - [Feedback Result](#feedback-result)
  - [Play Store link](#play-store-link)
    - [Reset passive forms](#reset-passive-forms)
  - [Custom http client](#custom-http-client)
  - [Custom variables](#custom-variables)
    - [Limitations](#limitations)
  - [UI Customisations](#ui-customisations)
    - [UsabillaTheme](#usabillatheme)
    - [Custom Images](#custom-images)
      - [Custom Emoticons Rating](#custom-emoticons-rating)
      - [Custom Star Rating](#custom-star-rating)
    - [Custom Fonts](#custom-fonts)
    - [Custom colors](#custom-colors)
    - [Apply different themes to different passive forms](#apply-different-themes-to-different-passive-forms)
  - [Localization](#localization)
  - [External Navigation](#external-navigation)
  - [Permissions](#permissions)
  - [Accessibility](#accessibility)

* * *

## Requirements
- The Usabilla SDK requires the minSdkVersion of the application to be 16 (Android 4.1).

## Android API limitations
Due to changes of the Android framework some minor aspects of the SDK will work differently based on the Android version running on the phone, specifically:

- The cursor on text fields will be tinted with the accent color only for API >= 21
- The custom drawables will be applied to the star component only for API >= 21
- The progress bar at the top of the form will be tinted with the accent color only for API >= 21

## Installation
- You can find the latest version of our SDK [here](https://bintray.com/usabilla/maven/ubform) and add it as a Maven or a Gradle dependency (`implementation 'com.usabilla.sdk:ubform:6.0.0'`).
- If you don't want to use a dependency manager you can also import the .aar library independently.
Our SDK uses the following dependencies. If your project doesn't use them already you might need to add it as well in your gradle file.
```
dependencies {
    implement 'com.android.volley:volley:1.1.1'
    implement 'com.android.support:appcompat-v7:28.0.0'
    implement "org.jetbrains.kotlin:kotlin-stdlib-jre7:1.3.11"
}
```

## Initialization
The Usabilla SDK should first of all be initialized using the **initialize** method in one of its four versions:

```java
Usabilla.initialize(Context context);
Usabilla.initialize(Context context, @Nullable String appId);
Usabilla.initialize(Context context, @Nullable String appId, @Nullable UsabillaHttpClient httpClient);
Usabilla.initialize(Context context, @Nullable String appId, @Nullable UsabillaHttpClient httpClient, @Nullable UsabillaReadyCallback callback);
```

The method **initialize** has three optional parameters:
- `appId` needs to be used if you want to use the Campaign feature (please read the [Campaigns](#campaigns) section for more information).
- `httpClient` gives the possibility to inject a custom client to handle all the connections performed by the SDK (please read the [Custom Http Client](#custom-http-client) section for more information).
- `callback` is a callback used to communicate when the initialization process ends.

If you are using Campaigns, the callback will indicate that the SDK is ready to receive events.

The **initialize** method will take care of:
* Submitting any pending feedback items.
* Fetching and updating all campaigns associated with the appId.
* Initializing a few background processes of the SDK.

>⚠️ **Failure to call this method before using the SDK will prevent it from running properly.**

### Debug Mode
In order to obtain more insights form the SDK while developing, logging can be enabled with:
```java
Usabilla.setDebugEnabled(true);
```

This property is by default set to `false`.

## Campaigns
This guide describes the Campaigns feature and all the steps necessary to work with it.

In the Usabilla for Apps Platform, a campaign is defined as a proactive survey targeted to a specific set of users.

Running Campaigns in your mobile app is great because it allows you to collect very specific insights from a targeted set of users. Furthermore, creating new Campaigns and managing existing ones can be done without the need for a new app release. Everything can be managed from the Usabilla web interface.

You can run as many campaigns as you like and target them to be triggered when a specific set of targeting options are met.
The configuration of how a campaign is displayed to the user will be familiar to existing Usabilla customers. You can configure it to suit your needs just like you are used to from the Passive feedback forms.

The most important aspect of running a mobile campaign is the presence of 'Events'. Events are custom triggers that are configured in the SDK. When a pre-defined event occurs, it will allow you to trigger a campaign. A good example of an event is a successful purchase by a user in your app.

### The App Id
The appId is an identifier used to associate campaigns to a mobile app. It is generated by Usabilla and can be found in the Usabilla web interface. By loading the SDK with a specific appId, it will fetch all the campaigns connected to it. If you need to know how to find the appId please take a look at our support article here: 

https://support.usabilla.com/hc/en-us/articles/360021618151-How-to-find-the-App-ID

We recommend you to use a separate AppId per app. One Campaign can be targeted to multiple apps (e.g. Android production app, Android beta app) by associating it with multiple appIds

### Targeting options
Campaigns are triggered by events. Events are used to communicate with the SDK when something happens in your app. Consequently, the SDK will react to an event depending on the configuration of the Usabilla web interface.
To send an event to the SDK, use :
```java
Usabilla.sendEvent(Context context, String event);
```

You can also segment your user base using custom variables.
**Custom variables** can be used to specify some traits of the user and target the campaign only to a specific subset.

For more on how to use custom variables, read [Custom Variables](#custom-variables)

**Note: A campaign will never be triggered for the same user more than once.**

### Managing an existing campaign
You can start collecting campaign results right after you create a new campaign in the Usabilla for Apps [Campaign Editor](https://app.usabilla.com/member/live/apps/campaigns/add).
By default, new campaigns are marked as inactive. On the Usabilla for Apps [Campaign Overview](https://app.usabilla.com/member/#/apps/campaigns/overview/) page, you can activate or deactivate an existing campaign at any moment to reflect your specific needs.

Moreover, you can update the content of your campaign (e.g. questions) at any time. Keep in mind that the changes you make to an existing active campaign might affect the integrity of the data you collect (different responses before and after a change).

Furthermore, you can also change the targeting options of a campaign. Keep in mind that updating the targeting options of an active campaign will **reset** any progression previously made on the user's device.

### Campaign results
Aggregated campaign results are available for download from the [Campaign Overview](https://app.usabilla.com/member/#/apps/campaigns/overview/). Here you can download the results per campaign, in the CSV format.

Campaign results will contain the answers that your users provided. Responses from a campaign are collected and sent to Usabilla page by page. This means that even if a user decides to abandon the campaign halfway through, you will still collect valuable insights. When a user continues to the next page, the results of the previous page are submitted to Usabilla. Besides campaign results showing the answers to your campaign questions, you will be able to view the device metadata and custom variables.

As for campaign results. Please note that editing the form of an existing campaign will affect the aggregated campaign results:

- Adding new questions to a form will add additional columns to the CSV file.
- Removing questions from an existing form will not affect the previously collected results. The associated column and its data will still be in the CSV file.
- Replacing the question type with a different question is also possible. When you give the same 'name' in the Usabilla for Apps Campaign Editor, the results are represented in the same column.

### Update fragment manager
⚠️ It's important to note that our campaigns use Android Fragments in order to be displayed to the user; therefore it's important that you provide a reference to the FragmentManager using:

```java
Usabilla.updateFragmentManager(FragmentManager fragmentManager);
```

Furthermore, remember to handle properly the device rotation and other cases where a new FragmentManager is created, in order to always provide the latest one to the SDK.

The FragmentManager accepted is the one coming from the support library with the path `android.support.v4.app.FragmentManager`

### Reset campaigns data
The Usabilla SDK offers the possibility to reset the campaign data stored locally using the **resetCampaignData** method in one of its two flavours:
```java
Usabilla.resetCampaignData(Context context);
Usabilla.resetCampaignData(Context context, @Nullable UsabillaReadyCallback callback);
```
The method removes all campaigns stored locally and fetches them again from our remote API, effectively losing any trace whether they triggered or not.
The optional parameter `callback` is used to communicate when the fetching of the campaigns has ended and the campaign events can start being processed by the Usabilla SDK.

## Passive feedback
Passive feedback are feedback forms that are not triggered by events.
They are mostly, but not necessarily, initiated directly by the user.

### Loading a form
The SDK uses the Form ID you get from [Usabilla](https://app.usabilla.com/member/apps/list) after creating a new form, to fetch and display the form inside your app.
The **loadFeedbackForm** method can be used in one of its four versions:

```java
Usabilla.loadFeedbackForm(String formId);
Usabilla.loadFeedbackForm(String formId, @Nullable Bitmap screenshot);
Usabilla.loadFeedbackForm(String formId, @Nullable Bitmap screenshot, @Nullable UsabillaHttpClient httpClient);
Usabilla.loadFeedbackForm(String formId, @Nullable Bitmap screenshot, @Nullable UsabillaHttpClient httpClient, @Nullable UsabillaFormCallback callback);
```

The method **loadFeedbackForm** has three optional parameters:
- `screenshot` is the bitmap that will be attached to the feedback form.
- `httpClient` gives the possibility to inject a custom client to handle all the connections performed by the SDK (please read the [Custom Http Client](#custom-http-client) section for more information).
- `callback` is a callback used to communicate when the loading ends.

A basic implementation of the SDK would be the following:

```java
public class MainActivity extends AppCompatActivity implements UsabillaFormCallback {

    public void loadForm() {
        // After initialising our SDK
        // The null parameters are for the screenshot and theme, both explained in their own chapters
        Usabilla.loadFeedbackForm("the form id to load", null, null, this);
    }

    @Override
    public void formLoadSuccess(FormClient form) {
        getSupportFragmentManager()
              .beginTransaction()
              .replace(R.id.main_activity_frame, form.getFragment(), FRAGMENT_TAG)
              .commit();
    }

    @Override
    public void formLoadFail() {
        // The form did not load (no internet connection, invalid formId...)
    }

    @Override
    public void mainButtonTextUpdated(String text) {
        // See section External Navigation
    }
}
```

### Preloading a form
Our SDK offers the possibility to preload forms. The form will be fetched and stored locally. This could be useful in case the user is offline and the form is requested.

```java
Usabilla.preloadFeedbackForms(List<String> formIds);
```

### Adding a screenshot
It is possible to attach a screenshot to a feedback form.

You can take a screenshot at any moment by calling

``` java
// Passing a View to take the screenshot and attach it the form
Bitmap myScreenshot = Usabilla.takeScreenshot(View view);

// Passing an Activity to take the screenshot and attach it the form
Bitmap myScreenshot = Usabilla.takeScreenshot(Activity activity);
```

These methods will return a bitmap containing a screenshot of the view or activity you have passed.

You can then attach this image, or any other bitmap of your choosing, to the feedback form you're loading by specifying the screenshot parameter in the `loadFeedbackForm` method.

```java
Bitmap myScreenshot = Usabilla.takeScreenshot(this);
Usabilla.loadFeedbackForm("formId", myScreenshot, null, this);
```

## Feedback submission callback
It is possible to know when the feedback form has been closed implementing the following `BroadcastReceiver`.
 * Note 1: The form closure happens both as a result of the user dismissing it half-way through or them completing it.
 * Note 2: A separate `BroadcastReceiver` can be set for both passive feedback and campaigns, depending on your needs.

```java
private BroadcastReceiver usabillaCloserPassive;
private IntentFilter closerFilterPassive = new IntentFilter(UbConstants.INTENT_CLOSE_FORM);
private BroadcastReceiver usabillaCloserCampaign;
private IntentFilter closerFilterCampaign = new IntentFilter(UbConstants.INTENT_CLOSE_CAMPAIGN);


@Override
protected void onStart() {
    super.onStart();
    LocalBroadcastManager.getInstance(this).registerReceiver(usabillaCloserPassive, closerFilterPassive);
    LocalBroadcastManager.getInstance(this).registerReceiver(usabillaCloserCampaign, closerFilterCampaign);
}

@Override
protected void onStop() {
    super.onStop();
    LocalBroadcastManager.getInstance(this).unregisterReceiver(usabillaCloserPassive);
    LocalBroadcastManager.getInstance(this).unregisterReceiver(usabillaCloserCampaign);
}

private void setupCloserBroadcastReceiver() {
    usabillaCloserPassive = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            // The passive feedback form has been closed and the feedback result is returned
            // The extra is INTENT_FEEDBACK_RESULT
            final FeedbackResult result = intent.getParcelableExtra(FeedbackResult.INTENT_FEEDBACK_RESULT);
        }
    };
    usabillaCloserCampaign = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            // The campaign feedback form has been closed and the feedback result is returned
            // The extra is INTENT_FEEDBACK_RESULT_CAMPAIGN
            final FeedbackResult result = intent.getParcelableExtra(FeedbackResult.INTENT_FEEDBACK_RESULT_CAMPAIGN);
        }
    };
}
```

### Feedback Result
```java
public class FeedbackResult {
    int rating;
    int abandonedPageIndex;
    boolean isSent;
}
```

The **rating** value is set as soon as the user interacts with it and will be reported even if the form is not submitted.

The **abandonedPageIndex** property is only set if the user cancels the form before submission, otherwise its calue is -1.

The **isSent** property is false if the user dropped out while filling the form.

## Play Store link
Enable "App Store redirect" in the Advanced panel of the feedback form editor in order to redirect the user to the Play Store after leaving a positive feedback item.

This will show a native dialog after the last page of the passive feedback form and after the campaign form was displayed. In both cases, they will be displayed only if the mood rating is 4 or 5.

### Reset passive forms
The Usabilla SDK offers the possibility to reset the database from previosuly fetched passive forms using the **removeCachedForms** method:

```java
Usabilla.removeCachedForms();
```
The method removes all passive forms stored locally.

## Custom http client
We allow the use of a custom client to handle all the connections to retrieve and send data through our SDK.

To do so we allow to specify a parameter in the `initialize` method adhering to the `UsabillaHttpClient` interface, which in turns depends on three other interfaces:

- `UsabillaHttpRequest`: encapsulating a HTTP request
- `UsabillaHttpResponse`: encapsulating a HTTP response
- `UsabillaHttpListener`: providing callbacks for success and failure of a request

A sample http client implementation done leveraging the library `OkHttp` can be seen in the class `CustomHttpClient.java`

## Custom variables
You can pass along custom variables that will be attached to the feedback users send.
Custom variables are held in a `HashMap<String, Object>` in the public interface of the SDK.

You can set custom variables by manipulating the `customVariables` object, adding or removing entries as you need. An example is as follow:

```java
// Setting a whole object
 HashMap<String, Object> myVariables = new HashMap<>();
 myVariables.put("tier", "premium");
 myVariables.put("loggedIn", true);
 Usabilla.setCustomVariables(myVariables);

 // Modifying the object
 Usabilla.getCustomVariables().put("tier", "premium");
 ```

Custom variables are added as extra feedback data with every feedback item sent by the SDK, whether from a passive feedback or a campaign.

⚠️ **Custom variables can be used as targeting options, as long as the `value` is a `String`.**

⚠️ **Usabilla.setCustomVariables overwrites the custom variables already present, it does not add them to the pre-existing ones**

### Limitations
There are a few limitations to the kind of objects you can add to the custom variables:

* You can add custom objects, but you will have to override the `toString()` method to be able to read them from the Usabilla dashboard.
* Arrays of objects are not accepted and will be removed by our servers
* The custom variables will be translated into a `JSONObject` during the submission to our servers, so any non parseable value will break their submission.

## UI Customisations

### UsabillaTheme
We provide a class `UsabillaTheme` containing two parameters of type `UbFonts` and `UbImages` in its constructor.

**They are both nullable, and if not provided will use the SDK default values as follows:**
- `UbFonts`:
  - text and title size 18sp
  - note text size 16dp
  - boldness enabled
- `UbImages`:
  - default mood and stars as seen in the Usabilla dashboard

### Custom Images
`UbImages` can be instantiated with four different parameters:
- `selectedEmoticons` is the list of resource IDs to be used as selected emoticons in place of the default ones for the Mood component.
- `unselectedEmoticons` is the list of resource IDs to be used as unselected emoticons in place of the default ones for the Mood component.
- `star` is the resource ID to be used in place of the default star for the Mood component (when in star version).
- `starOutline`is the resource ID to be used in place of the default star outline for the Mood component (when in star version).

#### Custom Emoticons Rating
To use custom emoticons you must provide two lists of **five image IDs** to be used instead of the Usabilla default emoticons (one list for their selected state and one for the unselected state).

The first element of the array should be the lowest or leftmost item, while the 5th element will be the highest or rightmost.

If only the list containing the selected version of the icons is provided, the images will be displayed with an alpha value of 0.5 when unselected, and with an alpha value of 1 when selected.

```java
// Create images
ArrayList<Integer> imagesIdList = new ArrayList<Integer>();
imagesIdList.add(R.drawable.your_mood_1);
imagesIdList.add(R.drawable.your_mood_2);
imagesIdList.add(R.drawable.your_mood_3);
imagesIdList.add(R.drawable.your_mood_4);
imagesIdList.add(R.drawable.your_mood_5);
UbImages themeImages = new UbImages(imagesIdList);

// Update theme
UsabillaTheme newTheme = new UsabillaTheme(null, themeImages);
Usabilla.setTheme(newTheme);
```

You can also provide two lists containing the selected and the unselected version of the icons.

```java
UbImages themeImages = new UbImages(selectedEmoticonsImagesIdList, unselectedEmoticonsImagesIdList);

// Update theme
UsabillaTheme newTheme = new UsabillaTheme(null, themeImages);
Usabilla.setTheme(newTheme);
```

#### Custom Star Rating
You can change the appearance of the star in the Star Rating by setting both `star` and `starOutline` in the `UbImages` object.

Keep in mind that, in order to display the Star Rating in your form, you must first enable it in the [Usabilla Web Interface](https://app.usabilla.com/member/apps/).

```java
UbImages themeImages = new UbImages(null, null, R.drawable.ic_star, R.drawable.ic_star_outline);

// Update theme
UsabillaTheme newTheme = new UsabillaTheme(null, themeImages);
Usabilla.setTheme(newTheme);
```

### Custom Fonts
`UbFonts` can be instantiated with five different parameters:
- `regular` is the resource IDs for the font to be applied to the feedback form.
- `bold` is enabling text to be shown bold (for titles for example).
- `titleSize` is the size in `sp` for the titles in the form
- `textSize` is the size in `sp` for the text in the form
- `miniSize` is the size in `sp` for the small text in the form (e.g. NPS text, Error labels or top page required field note)

⚠️ The font resource needs to be placed in the `res/fonts` folder of the project and be in format `.otf` or `.ttf`

```java
UbFonts themeFonts = UbFonts(R.font.name_of_your_font)

// Update theme
UsabillaTheme newTheme = new UsabillaTheme(themeFonts, null);
Usabilla.setTheme(newTheme);
```

### Custom colors
All colors are set from the Usabilla website and not from the SDK. You can find a detailed explanation of the color's name and role in our [knowledge base](https://support.usabilla.com/hc/en-us/articles/211588989-How-do-I-change-the-feedback-form-colors-in-Usabilla-for-Apps).

### Apply different themes to different passive forms
Setting a general theme will apply it to both passive forms and campaign forms.

Additionally, it is possible to specify a theme when requesting a passive form, which will be solely applied to the form upon receiving it.

This can be done as follows:

```java
UbFonts themeFonts = UbFonts(R.font.name_of_your_font)
UbImages themeImages = new UbImages(null, null, R.drawable.ic_star, R.drawable.ic_star_outline);
UsabillaTheme newTheme = new UsabillaTheme(themeFonts, themeImages);
Usabilla.loadFeedbackForm(formId, null, newTheme, this);
```

Multiple requests of multiple forms done with different themes will result in each form having its own dedicated theme.

## Localization
If you want to provide your own translation, you need to override the strings in the default Usabilla SDK. You can do so by providing a string with the same name in your main application string resource file. This will override the SDK default value and yours will be displayed instead.

The string resources you can override and their default value are the following
```xml
<string name="ub_field_error">Please check this field</string>
<string name="ub_element_screenshot_title">Screenshot (optional)</string>
<string name="ub_element_screenshot_message">Add screenshot</string>
<string name="ub_button_close_default">Close</string>
<string name="ub_button_continue_default">Continue</string>
<string name="ub_button_playstore_default">Rate on the play store</string>
<string name="ub_button_submit_default">Submit</string>
<string name="ub_dialog_playStore_title">Rate</string>
<string name="ub_dialog_playstore_message">Thank you for your feedback! Would you like to leave a review?</string>
<string name="ub_dialog_playstore_negative">No, thanks</string>
<string name="ub_dialog_playstore_positive">Rate now</string>
<string name="ub_sdk_permission_disabled_label">Permission disabled!\nEnable it from Settings -> app info</string>
<string name="ub_element_mood_select_rating">Select a rating out of %1$d</string>
<string name="ub_element_slider_select_rating">Select a rating from %1$d (%2$s) to %3$d (%4$s)</string>
<string name="ub_element_required">This field is required</string>
<string name="ub_element_screenshot_delete">Delete Screenshot</string>
<string name="ub_element_screenshot_edit">Change Screenshot</string>
<string name="ub_element_mood_hate">I really don\'t like it!</string>
<string name="ub_element_mood_dislike">I don\'t like it</string>
<string name="ub_element_mood_neutral">I feel neutral</string>
<string name="ub_element_mood_like">I like it!</string>
<string name="ub_element_mood_love">I love it!</string>
<string name="ub_element_mood_one_star">One star</string>
<string name="ub_element_mood_two_star">Two stars</string>
<string name="ub_element_mood_three_star">Three stars</string>
<string name="ub_element_mood_four_star">Four stars</string>
<string name="ub_element_mood_five_star">Five stars</string>
```

## External Navigation
It is possible to hide the default navigation buttons our forms use (continue and cancel button) from the SDK and provide your own (e.g. in the Toolbar).

To do so a couple of steps are required:
* Set the standard navigation buttons invisible
    ```java
    Usabilla.setDefaultNavigationButtonsVisibility(false);
    ```
* Call the method `navigationButtonPushed` from the `onClickListener` of your custom button (e.g. the one you have placed in the Toolbar) to let the form know that it was pressed. ⚠️ This is only associating that click to a 'continue' action, you should provide handling for the cancellation yourself.
    ```java
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            ...
            case R.id.myNextButtonForForms:
                form.navigationButtonPushed();
                break;
            ...
        }
        return true;
    }
    ```
* (OPTIONAL) Use the `mainButtonTextUpdated` to grab the text for the navigation button you want to use
    ```java
    @Override
    public void mainButtonTextUpdated(String text) {
        // Use this text for your own navigation button
        // It returns "Next" or "Submit"
    }
    ```

## Permissions
If the user tries to set a custom screenshot, the SDK will ask for the permission to access the external storage.    
No other permission is needed to run the SDK.

## Accessibility
In order to have Android TalkBack work properly with the passive feedback form, you have to make sure that the `Activity` (or `Fragment`) holding the `FormClient.fragment` received from our SDK stops being considered for TalkBack.

This can be achieved using this line
```java
view.setImportantForAccessibility(View.IMPORTANT_FOR_ACCESSIBILITY_NO_HIDE_DESCENDANTS);
```
where `view` is the `ViewGroup` you want to exclude from the TalkBack.

Of course when the form is dismissed and you proceed to remove it from its holding `Activity` (or `Fragment`) then you can reset the TalkBack to consider it with the line
```java
view.setImportantForAccessibility(View.IMPORTANT_FOR_ACCESSIBILITY_YES);
```

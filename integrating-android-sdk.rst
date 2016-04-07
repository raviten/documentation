Android SDK integration
=======================

Installation in Android Studio
------------------------------

#. Add dependencies to *app/build.gradle*::

    compile "com.google.android.gms:play-services:8.1.0"
    compile "com.quantumgraph.sdk:QG:1.1.11"

#. If you would like to reach out to uninstalled users by email, add following line in *app/src/main/AndroidManifest.xml* outside the *<application>* tag::

   <uses-permission android:name="android.permission.GET_ACCOUNTS" />

#. If you would like us to track the city of the user, add the following line in *app/src/main/AndroidManifest.xml* outside the *<application>* tag::

   <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
   <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

#. If you would like us to track device id the user, add the following line in *app/src/main/AndroidManifest.xml* outside the *<application>* tag::

   <uses-permission android:name="android.permission.READ_PHONE_STATE" />

Installation in Eclipse 
-----------------------
To integrate our SDK in eclipse, these are the steps:

#. Add following permissions in *app/src/main/AndroidManifest.xml* outside the *<application>* tag::

    <uses-permission android:name="com.google.android.c2dm.permission.SEND" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="com.google.android.c2dm.intent.REGISTER" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.VIBRATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

#. If you would like to reach out to uninstalled users by email, add following line in *app/src/main/AndroidManifest.xml* outside the *<application>* tag::

   <uses-permission android:name="android.permission.GET_ACCOUNTS" />

#. If you would like us to track the city of the user, add the following line in *app/src/main/AndroidManifest.xml* outside the *<application>* tag::

   <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
   <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

#. If you would like us to track device id the user, add the following line in *app/src/main/AndroidManifest.xml* outside the *<application>* tag::

   <uses-permission android:name="android.permission.READ_PHONE_STATE" />

#. Add following lines in *app/src/main/AndroidManifest.xml* inside the *<application>* tag::

    <receiver
       android:name="com.google.android.gms.gcm.GcmReceiver"
       android:exported="true"
       android:permission="com.google.android.c2dm.permission.SEND" >
       <intent-filter>
          <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
          <action android:name="com.google.android.c2dm.intent.RECEIVE" />
          <category android:name="PACKAGE_NAME_OF_YOUR_APP" />
       </intent-filter>
    </receiver>
    <service
       android:name="com.quantumgraph.sdk.QGInstanceIdListener"
       android:exported="false">
       <intent-filter>
          <!-- Receives the Refresh token message. -->
          <action android:name="com.google.android.gms.iid.InstanceID"/>
       </intent-filter>
    </service>
    <service
       android:name="com.quantumgraph.sdk.QGGcmListenerService"
       android:exported="false">
       <intent-filter>
          <!-- Receives the actual messages. -->
          <action android:name="com.google.android.c2dm.intent.RECEIVE" />
       </intent-filter>
    </service>
    
    <receiver
       android:name="com.quantumgraph.sdk.NotificationIntentProcessor"
       android:enabled="true"
       android:exported="true">
    </receiver>
    <service
       android:name="com.quantumgraph.sdk.GcmNotificationIntentService"
       android:exported="true">
    </service>

#. Next, download the eclipse SDK as a library project from http://app.qgraph.io/static/sdk/android/QG-1.1.11.zip

#. Now you can proceed in one of the two ways:
   Either import this library project in eclipse in the same work space where your app resides. Then add QG project as a dependency in your project. 

   or else, (a) unzip this file and import classes.jar in *libs/* directory of your project (b) import classes.jar in your project (c) copy *layouts/qg_slider_or_carousel.xml*
   and *layout/qg_full_content_view.xml* from the unzipped directory to *layout/* directory of your project (d) copy *drawable/avatar.png*, *drawable/qg_close_button.png*,
   *drawable/qg_inapp_callout_big_9.png*, *drawable/qg_next_button.png*, *drawable/qg_prev_button.png* from the unzipped
   directory to *drawable/* directory of your project.

Using Android SDK
-----------------
Follow these steps to use our android SDK

Import QG SDK in your activity
##############################
In all the activity classes, starting with the class for the main activity, import QG SDK::

   import com.quantumgraph.sdk.QG;

Initialization and cleanup of SDK
#################################
#. Define a variable called ``qg`` in your activity::
   
    private QG qg;
   
#. Add a line in ``onCreate()`` of your activity.  If you want to use QGraph's Sender Id and GCM key, add the following::
    
    QG.initializeSdk(getApplication(), <your app id>);

   If you want to use your Sender Id and GCM key, add the following::

    QG.initializeSdk(getApplication(), <your app id>, <your sender id>);

   App id for your app is available from the settings page. Sender id is a string that Google provides to you for getting registration id for users. You will get the sender id for your app during the set up phase in our web interface.

#. In the ``onStart()`` function of your activity, add the following::

    qg = QG.getInstance(getApplicationContext());
    qg.onStart();

We automatically track email, location and installed apps of your user. If you want to stop
any of these trackings::

    qg.setTracking(bool trackEmail, bool trackLocation, bool trackInstalledApps);

For instance, if you want to track email of your user, but not location or installed apps, you
call::

    qg.setTracking(true, false, false);


Logging user profiles
#####################
User profiles are information about your users, like their name, city, date of birth
or any other information that you may wish to track. You log user profiles by using one or more of the following functions::

   qg.setUserId(String userId);

userId is the id of the user. It might be email, or username, or facebook id, or any other form of id that you may wish to keep.

Other functions that you may use are::

   qg.setName(String name);
   qg.setFirstName(String firstName);
   qg.setLastName(String lastName);
   qg.setCity(String city);
   qg.setEmail(String email);
   qg.setDayOfBirth(int day);
   qg.setMonthOfBirth(int month);
   qg.setYearOfBirth(int year);
   qg.setPhoneNumber(String phoneNo);

Other than these functions, you can log your own custom user parameters. You do it using::

   qg.setCustomUserParameter(String key, E value);

For instance, you may wish to have the user's current rating like this::

   qg.setCustomUserParameter("current_rating", 123);

As implied by the function definition, the value can be of any data type.

If you want to set multiple custom parameters at once, you can use ``qg.setCustomUserParameters()``. For example::

   JSONObject jsonObject = new JSONObject();
   jsonObject.put("gender", "male");
   jsonObject.put("age", 23);
   jsonObject.put("city", "London");
   qg.setCustomUserParameters(jsonObject);

Once user profile is set, you can use this to create personalized messages (For example: "Hi John, exciting deals are available in Mountain View"), or to create user segments (For example you can create a segment of users who were born after 1990 and live in Mountain View)

Apart from above user profile parameters, you can log the UTM source through which the user installed the app, using the following functions::

   qg.setUtmSource(String utmSource);
   qg.setUtmMedium(String utmMedium);
   qg.setUtmTerm(String utmTerm);
   qg.setUtmContent(String utmContent);
   qg.setUtmCampaign(String utmCampaign);

Logging events
##############
Events are the activities that a user performs in your app, for example, view the products, playing a game or listening to a music. Each event has a name (for instance, the event of viewing a product is called ``product_viewed``), and can have some parameters. For instance, 
for event ``product_viewed``, the parameters are ``id`` (the id of the product viewed), ``name`` (name of the product viewed), ``image_url`` (image url of the product viewed), ``deep_link`` (a deep link which takes one to the product page in the app), and so on.

It is not necessary that you provide all the parameters for a given event. You can choose to provide whatever parameters are relevant to you.

Once you log event information to use, you can segment users on the basis of the events (For example, you can create a segment consisting of users have not launched for past 7 days, or you can create a segment consiting of users who, in last 7 days, have purchased a product whose value is more than $1000)

You can also define your events, and your own parameters for any event. However, if you do that, you will need to sync up with us to be able to segment the users on the basis of these events or customize your creatives based on these events.

You can optionally log a "value to sum" with an event. This value will be summed up when doing campaing attribution. For instance, if you pass this value in your checkout completed event, you will be able to view stats such as a particular campaign has been responsible to drive Rs 84,000 worth of sales.

Thus, there are three variants of the function ``logEvent()`` which logs the event

* ``logEvent(String eventName)``

* ``logEvent(String eventName, JSONObject parameters)``

* ``logEvent(String eventName, JSONObject parameters, double valueToSum)``

Here is how you set up some of the popular events.

**Registration Completed**

This event does not have any parameters::

   QG qg = QG.getInstance(getApplicationContext());
   JSONObject registrationDetails = new JSONObject();
   try {
      qg.logEvent("registration_completed", registrationDetails);
   } catch (JSONException e) {
   }

**Category Viewed**

This event has one paraemter::

   QG qg = QG.getInstance(getApplicationContext());
   JSONObject categoryDetails = new JSONObject();
   try {
      categoryDetails.put("category", "apparels");
   } catch (JsonException e) {
   }
   qg.logEvent("category_viewed", categoryDetails);

**Product Viewed**

You may choose to have the following fields::

   QG qg = QG.getInstance(getApplicationContext());
   JSONObject productDetails = new JSONObject();
   try {
      productDetails.put("id", "123");
      productDetails.put("name", "Nikon Camera");
      productDetails.put("image_url", "http://mysite.com/products/123.png");
      productDetails.put("deep_link", "myapp//products?id=123");
      productDetails.put("type", "new");
      productDetails.put("category", "electronics");
      productDetails.put("brand", "Nikon");
      productDetails.put("color", "white");
      productDetails.put("size", "small");
      productDetails.put("price", 6999);
   } catch (JsonException e) {
   }
   qg.logEvent("product_viewed", productDetails);

**Product Added to Cart**::

   QG qg = QG.getInstance(getApplicationContext());
   JSONObject productDetails = new JSONObject();
   try {
      productDetails.put("id", "123");
      productDetails.put("name", "Nikon Camera");
      productDetails.put("image_url", "http://mysite.com/products/123.png");
      productDetails.put("deep_link", "myapp//products?id=123");
      productDetails.put("type", "new");
      productDetails.put("category", "electronics");
      productDetails.put("brand", "Nikon");
      productDetails.put("color", "white");
      productDetails.put("size", "small");
      productDetails.put("price", 6999);
   } catch (JsonException e) {
   }
   qg.logEvent("product_added_to_cart", productDetails);

**Product Added to Wishlist**::

   QG qg = QG.getInstance(getApplicationContext());
   JSONObject productDetails = new JSONObject();
   try {
      productDetails.put("id", "123");
      productDetails.put("name", "Nikon Camera");
      productDetails.put("image_url", "http://mysite.com/products/123.png");
      productDetails.put("deep_link", "myapp//products?id=123");
      productDetails.put("type", "new");
      productDetails.put("category", "electronics");
      productDetails.put("brand", "Nikon");
      productDetails.put("color", "white");
      productDetails.put("size", "small");
      productDetails.put("price", 6999);
   } catch (JsonException e) {
   }
   qg.logEvent("product_added_to_wishlist", productDetails);


**Product Purchased**::

   QG qg = QG.getInstance(getApplicationContext());
   JSONObject productDetails = new JSONObject();
   try {
      productDetails.put("id", "123");
      productDetails.put("name", "Nikon Camera");
      productDetails.put("image_url", "http://mysite.com/products/123.png");
      productDetails.put("deep_link", "myapp//products?id=123");
      productDetails.put("type", "new");
      productDetails.put("category", "electronics");
      productDetails.put("brand", "Nikon");
      productDetails.put("color", "white");
      productDetails.put("size", "small");
      productDetails.put("price", 6999);
   } catch (JsonException e) {
   }
   qg.logEvent("product_purchased", productDetails, 6999);
   /* Or if you do not want to pass the third argument, you can simply write
   qg.logEvent("product_purchased", productDetails);*/


**Checkout Initiated**::

   QG qg = QG.getInstance(getApplicationContext());
   JSONObject checkoutDetails = new JSONObject();
   try {
      checkoutDetails.put("num_products", 2);
      checkoutDetails.put("cart_value", 12998.44);
      checkoutDetails.put("deep_link", "myapp://myapp/cart");
   } catch (JsonException e) {
   }
   qg.logEvent("checkout_initiated", checkoutDetails);

**Checkout Completed**::

   QG qg = QG.getInstance(getApplicationContext());
   JSONObject checkoutCompleted = new JSONObject();
   try {
      checkoutDetails.put("num_products", 2);
      checkoutDetails.put("cart_value", 12998.44);
      checkoutDetails.put("deep_link", "myapp://myapp/cart");
   } catch (JsonException e) {
   }
   qg.logEvent("checkout_completed", checkoutDetails, 12998.44);
   /* Or if you do not want to pass the third argument, you can simply write
   qg.logEvent("product_purchased", productDetails);*/

**Product Rated**::

   QG qg = QG.getInstance(getApplicationContext());
   JSONObject rating = new JSONObject();
   try {
      rating.put("id", "1232");
      rating.put("rating", 2);
   } catch (JsonException e) {
   }
   qg.logEvent("product_rated", rating);

**Searched**::

   QG qg = QG.getInstance(getApplicationContext());
   JSONObject search = new JSONObject();
   try {
      search.put("id", "1232");
      search.put("rating", 2);
   } catch (JsonException e) {
   }
   qg.logEvent("product_rated", rating);

**Reached Level**::

   QG qg = QG.getInstance(getApplicationContext());
   JSONObject level = new JSONObject();
   try {
      level.put("level", 23);
   } catch (JsonException e) {
   }
   qg.logEvent("level", rating);

**Your custom events**

Apart from above predefined events, you can create your own custom events, and
have custom parameters in them::

   QG qg = QG.getInstance(getApplicationContext());
   JSONObject json = new JSONObject();
   try {
      json.put("my_param", "some value");
      json.put("some_other_param", 123);
      json.put("what_ever", 1234.23);
   } catch (JsonException e) {
   }
   qg.logEvent("my_custom_event", json);


**Logging mulitple events together**

You can launch multiple events together. You call ``createEvent()`` to create an event. Here we log four events together. This example illustrates four ways in which we can create events::

   QG qg = QG.getInstance(getApplicationContext());
   JSONObject eventParams = newJSONObject();
   eventParams.put("param1", "val");
   eventParams.put("param2", 123);
   JSONArray eventsArray = new JSONArray();
   eventsArray.put(qg.createEvent("eventA"));
   eventsArray.put(qg.createEvent("eventB", 123.12));
   eventsArray.put(qg.createEvent("eventC", eventParams, 12.12));
   eventsArray.put(qg.createEvent("eventD", eventParams));
   qg.logEvents(eventsArray);

Retrieving stored notifications
###############################
We provide the facility to store the notifications that you send. To enable notification
storage, please contact us at app@qgraph.io. We automatically store the notifications
which arrive at the SDK, and you can access them at any point of time. Here is how
you access stored notifications::

   JSONArray storedNotifications = QG.getStoredNotification();

Different notifications have different fields. All of them have a ``title`` and
``message``. They may also have ``imageUrl`` (URL of icon image), ``bigImageUrl``
(URL of the big image), ``deepLink`` and some other fields depending on the type
of the notification.


Notification checklist
----------------------
Launcher image
##############
Make sure that you have an image called ``ic_launcher.png`` in your ``drawables/`` folder.
We use this image to display as icon image if you don't set an icon image explicitly.
This image should be 192px x 192px or larger, with an aspect ratio of 1:1.

Notification image
##################
Make sure that you have an image called ``ic_notification.png`` in your ``drawables/`` foler.
This is the image shown in the status bar when a notification arrives. As per Android
guidelines (http://developer.android.com/design/patterns/notifications.html) this image should
be a white image on a transparent background. The size of this image should be 72px x 72px or
larger, with an aspect ratio of 1:1. This is what ic_notification.png should look like:
https://developer.android.com/samples/MediaBrowserService/res/drawable-hdpi/ic_notification.png

Recommended sizes of campaign images
####################################
When creating a campaign, you can set an icon image or a big image (or both). 

Icon image should be 192px x 192px or larger, with aspect ratio of 1:1.

Big image should be 1024px x 512px or larger, with aspect ratio close to 2:1.

If you use smaller images, then on some devices, the images may not be able to occupy complete area and hence there may be white spaces surrounding the images.

If you use your own Broadcast Receiver
######################################
QG SDK uses its own ``BroadcastReceiver``. In case you user your own ``BroadcastReceiver``
you will need to ignore the messages sent by QGraph. We provide you a helper method
``isQGMessage()`` to accomplish this. You need to include the following code in the
``onHandleIntent()`` method of the ``IntentService`` associated with the ``BroadcastReceiver``::

    @override
    protected void onHandleIntent(Intent intent) {
        Bundle extras = intent.getExtras()
        /* If the message is from QGraph, its intent handler 
           will handle it, and you should ignore the message. */
       if (extras.containsKey("message") && QG.isQGMessage(extras.getString("message"))) {
           return;
       }
    }
    
Similarly, QGraph's ``IntentService`` too ignores any messages that have not originated
from QGraph servers.

Receiving key value pairs in activity
#####################################
If you have set key value pairs in the campaign you can get them in the activity. Let's say
you passed a key valled ``myKey`` in the campaign, then you can get its value as following::

   @override
   protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      Intent intent = getIntent();
      Bundle bundle = intent.getExtras();
      String val = null;
      if (bundle != null) {
          val = bundle.getString("myKey");
      }

      /* More code */
   }

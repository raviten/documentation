iOS SDK integration
===================
For using quantumgraph iOS-SDK, do the following steps.

#. Integrate our quantumgraph iOS-SDK

#. Generate a PEM file

#. Using iOS-SDK

Installing iOS SDK Library
--------------------------

Using Cocoapods
###############

The easiest way to integrate quantumgraph iOS SDK into your iOS project is to use CocoaPods.


#. Install CocoaPods using ``gem install cocoapods``

#. If you are using Cocoapods for the first time, run ``pod setup`` to create a local CocoaPods spec mirror.

#. Create a file named ``Podfile`` in your Xcode project directory and add the following line in it::

     pod 'quantumgraph'

#. Run ``pod install`` in Xcode project directory. Cocoapods will downloads and install the quantumgraph iOS-SDK library and create a new Xcode workspace. From now on you should use this workspace.

Manual installation
###################

Download the SDK from
   http://app.qgraph.io/static/sdk/ios/QGSdk-1.8.1.zip

#. In your Xcode project, Go to File, add new Group to your project and name it as QGSdk.

#. Add libQSdk.a and QGSdk.h in QGSdk group 

Installation steps for Swift apps
#################################

#. In xcode, create the header file and name it by your product module name followed by adding ``-Bridging-Header.h``. File name should look like ``Project_Name-Bridging-Header.h``. Please make sure this header file is in root path of the project (although you can keep it anywhere).

#. Now Click on project tab to open Build Settings. In your project target -> Build Setting, search for ‘Objective-C Bridging Header’ and add path of the Project_Name-Bridging-Header.h. (Project_Name/Project_Name-Bridging-Header.h)

   .. figure:: swift-bridging-header.png
      :align: center

#. Import SDK header file in the bridging header file. Your file should look like this::
       
    #ifndef Project_Name_Bridging_Header_h
    #define Project_Name_Bridging_Header_h
    #import "QGSdk.h"
    #endif /* Project_Name_Bridging_Header_h */

Generating PEM file
-------------------
Generating App ID and SSL Certificate.
######################################

#. Log in to the iOS Dev Center and select the “Certificates, Identifiers and Profiles”
#. Select Certificates in the iOS Apps section
#. Go to App IDs in the sidebar and click the + button
#. Fill the details for App ID, App Services (Check the push notification Checkbox) and Explicit App ID(Should be same as Bundle ID in your App)
#. You will be asked to verify the details of the app id, if everything seems okay ckick Submit
#. In iOS App section, on the left, select App ID
#. Select your App ID
#. In the "Push Notification" row there are two orange lights that say "Configurable" in the Development and Distribution column
#. Click on the Settings button to configure these settings
#. Scroll down to the Push Notification section and select the Create Certificate in the Development/Production SSL Certificate
#. In the next step it will ask you for generating a CSR

Generating the Certificate Signing Request.
###########################################

#. Open Keychain Access on your Mac and choose the menu option Certificate Assistant -> Request a Certificate from a Certificate Authority
#. Enter some descriptive name for Common Name (Give your app name preferably)
#. Check Saved to disk option and click continue
#. In the Keys section of the Keychain Access, a new private key would have appeared
#. Save the private key file as keyfile.p12 in a separate folder
#. Choose the CSR that you generated
#. Click Continue and download the certificate
#. Save the certificate as cert.cer

**For generating pem file, you will require private key as keyfile.p12 file, SSL certificate (cert.cer)**

Convert the cert.cer file into a cert.pem file::

   $ openssl x509 -in cert.cer -inform der -out cert.pem;

Convert the keyfile.p12 file into a keyfile.pem file::

   $ openssl pkcs12 -nocerts -out keyfile.pem -in keyfile.p12;

combine the certificate and key into a single your_app_name.pem file::

   $ cat cert.pem keyfile.pem > your_app_name.pem;

Finally send us your_app_name.pem file 

Making the Provisioning Profile
###############################

#. Log in to the iOS Dev Center and select the “Certificates, Identifiers and Profiles”

#.  Click the Provisioning Profiles button in the sidebar and click the + button. This will open up the iOS profile wizard

#. Select the type of provisioning profile you need(Development/Distribution)

#. Select your App ID for your app and click continue.

#. Select the certificate you wish to include in the provisioning profile and click continue.

#. Give your App name as Profile Name and click Generate.


Using iOS Sdk
-------------
Changes in info.plist file
##########################

To allow the app to send data, you need to add following property in the info.plist file.

In ‘Information Property List’ click on ‘+’ to add ‘App Transport Security Settings’ which is a dictionary. Now click on this dictionary to add one item. Add Boolean ‘Allow Arbitrary Loads’ and set its value to YES.

   .. figure:: transport-security-settings.png
      :align: center

You may get following exception if above is not configured::

   Transport security has blocked a cleartext HTTP (http://) resource load since it is insecure. Temporary exceptions can be configured via your app's Info.plist file.


Appdelegate Changes for Objective C apps
########################################

To initialise the library, in Appdelegate  add ``#import "QGSdk.h"``

In ``didFinishLaunchingWithOptions`` method of Appdelegate, add the following code for registering for remote notification::

  (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
      if (floor(NSFoundationVersionNumber) < NSFoundationVersionNumber_iOS_8_0) {
          // here you go with iOS 7
          [[UIApplication sharedApplication] registerForRemoteNotificationTypes: (UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
      } else {
          // registering push notification in ios 8 and above
          UIUserNotificationType types = UIUserNotificationTypeAlert | UIUserNotificationTypeSound |
          UIUserNotificationTypeBadge;
          UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:types
          categories:nil];
          [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
          [[UIApplication sharedApplication] registerForRemoteNotifications];
      }
      //replace <your app id> with the one you received from QGraph
      [[QGSdk getSharedInstance] onStart:@"<YOUR APP ID>" setDevProfile:NO];
      [[QGSdk getSharedInstance] logEvent:@"app_launched" withParameters:nil];
      //add this method to track app launch through QGraph notification click 
      [[QGSdk getSharedInstance] didFinishLaunchingWithOptions:launchOptions];
  
      return YES;
  }


For development profile, set Boolean to YES in the following method::

   [[QGSdk getSharedInstance] onStart:@"<your app id>" setDevProfile:YES];


Just build and run the app to make sure that you receive a message that app would like to send push notification. If you get code signing error, make sure that proper provisioning profile is selected


Add the following code in Appdelegate.m to get the device token for the user::

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
            NSLog(@"My token is: %@", deviceToken);
            [[QGSdk getSharedInstance] setToken:deviceToken];
    }

    - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
    {
     	    NSLog(@"Failed to get token, error: %@", error);
    }

QGSdk ``setToken`` method will log user's token so that you can send push notification to the user

Add following code for tracking notification clicks::

   - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
      [[QGSdk getSharedInstance] didReceiveRemoteNotification:userInfo];
   }


Appdelegate Changes for Swift Apps
##################################

Please make following changes in your AppDelegate.swift file::

   func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
      // Override point for customization after application launch.
      // Register for remote notification
      let settings = UIUserNotificationSettings(forTypes: [.Alert, .Badge, .Sound], categories: nil)
      UIApplication.sharedApplication().registerUserNotificationSettings(settings)
      UIApplication.sharedApplication().registerForRemoteNotifications()
   
      let QG = QGSdk.getSharedInstance()
      QG.onStart("your_app_id")
      QG.logEvent("app_launched", withParameters: nil)
      QG.setName("user_name")
      
      // to enable tracking app launch by qgraph notification click
      QG.didFinishLaunchingWithOptions(launchOptions)
     
      return true;
    }

    func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData) {
        NSLog("My token is: %@", deviceToken)
        QG.setToken(deviceToken)
    }

    func application(application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: NSError) {
        print("Failed to get token, error: %@", error.localizedDescription)
    }

    func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject]) {
        let QG = QGSdk.getSharedInstance()
        // to enable track click on notification
        QG.didReceiveRemoteNotification(userInfo)
    }

Registering Your Actionable Notification Types
##############################################
Actionable notifications let you add custom action buttons to the standard iOS interfaces for local and push notifications. Actionable notifications give the user a quick and easy way to perform relevant tasks in response to a notification. Prior to iOS 8, user notifications had only one default action. In iOS 8 and later, the lock screen, notification banners, and notification entries in Notification Center can display one or two custom actions. Modal alerts can display up to four. When the user selects a custom action, iOS notifies your app so that you can perform the task associated with that action.

For defining a notification action and its category, and to handle actionable notification, please refer the description in the apple docs.

Action Category can be set in the dashboard while sending notification. While configuring to send notification through campaigns, use the categories defined in the app.

Handling Push Notification
##########################

Notifications are delivered while the app is in foreground, background or not running state. We can handle them in the following delegate methods. 
If the remote notification is tapped, the system launches the app and the app calls its delegate’s application:didFinishLaunchingWithOptions: method, passing in the notification payload (for remote notifications). Although application:didFinishLaunchingWithOptions: isn’t the best place to handle the notification, getting the payload at this point gives you the opportunity to start the update process before your handler method is called.

For remote notifications, the system also calls the ``application:didReceiveRemoteNotification:fetchCompletionHandler:`` method of the app delegate.

You can handle the notification and its payload as described::

   - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
      // Please make sure you have added this method of the sdk earlier. 
      [[QGSdk getSharedInstance] didFinishLaunchingWithOptions:launchOptions];
      // Payload can be handled in this way
      NSDictionary *notification = [launchOptions objectForKey:UIApplicationLaunchOptionsRemoteNotificationKey];
      if (notification) {
         // you custom methods…
      }
      return YES;
   }

The notification is delivered when the app is running in the foreground. The app calls the ``application:didReceiveRemoteNotification:fetchCompletionHandler:`` method of the app delegate. (If ``application:didReceiveRemoteNotification:fetchCompletionHandler:`` isn't implemented, the system calls ``application:didReceiveRemoteNotification:``.) However, it is advised to use application:didReceiveRemoteNotification:fetchCompletionHandler: method to handle push notification::

  - (void)application:(UIApplication *)application
     didReceiveRemoteNotification:(NSDictionary *)userInfo
     fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
     // Please make sure you add this method
     [[QGSdk getSharedInstance] didReceiveRemoteNotification:userInfo];
     handler(UIBackgroundFetchResultNoData);
     NSLog(@"Notification Delivered”);
     }

You can also handle background operation using the above method once remote notification is delivered. For this make sure, wake app in background is selected while creating a campaign to send the notification.

If you have implemented ``application:didReceiveLocalNotification:`` add method ``[[QGSdk getSharedInstance] didReceiveRemoteNotification:userInfo];`` inside it. Your implementation should look like::

   - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
      [[QGSdk getSharedInstance] didReceiveRemoteNotification:userInfo];
   }

Logging user profile information
################################

User profiles are information about your users, like their name, city, date of birth or any other information that you may wish to track. You log user profiles by using one or more of the following functions::
     
    - (void)setUserId:(NSString *)userId;

Other methods you may use to pass user profile prameters to us::

    - (void)setUserId:(NSString *)userId;
    - (void)setName:(NSString *)name;
    - (void)setFirstName:(NSString *)name;
    - (void)setLastName:(NSString *)name;
    - (void)setCity:(NSString *)city;
    - (void)setEmail:(NSString *)email;
    - (void)setDayOfBirth:(NSNumber *)day;
    - (void)setMonthOfBirth:(NSNumber *)month;
    - (void)setYearOfBirth:(NSNumber *)year;

Other than these method, you can log your own custom user parameters. You do it using::

    - (void)setCustomKey:(NSString *)key withValue:(id)value;

For example, you may wish to have the user's current rating like this::

    [[QGSdk getSharedInstance] setCustomKey:@"current rating" withValue:@"123"];


Logging events information
##########################
Events are the activities that a user performs in your app, for example, viewing the products, playing a game or listening to a music. Each event has follow properties:

1. Name. For instance, the event of viewing a product is called ``product_viewed`` 

2. Optionally, some parameters. For instance, for event ``product_viewed``, the parameters are ``id`` (the id of the product viewed), ``name`` (name of the product viewed), ``image_url`` (image url of the product viewed), ``deep_link`` (a deep link which takes one to the product page in the app), and so on.

3. Optionally, a "value to sum". This value will be summed up when doing campaing attribution. For instance, if you pass this value in your checkout completed event, you will be able to view stats such as a particular campaign has been responsible to drive Rs 84,000 worth of sales.

You log events using the function ``logEvent()``. It comes in four variations

* ``(void)logEvent:(NSString *)name``
* ``(void)logEvent:(NSString *)name withParameters:(NSDictionary *)parameters``
* ``(void)logEvent:(NSString *)name withValueToSum:(NSNumber *) valueToSum``
* ``(void)logEvent:(NSString *)name withParameters:(NSDictionary *)parameters``
        ``withValueToSum:(NSNumber *) valueToSum``


Once you log event information to use, you can segment users on the basis of the events (For example, you can create a segment consisting of users have not launched for past 7 days, or you can create a segment consiting of users who, in last 7 days, have purchased a product whose value is more than $1000)

You can also define your events, and your own parameters for any event. However, if you do that, you will need to sync up with us to be able to segment the users on the basis of these events or customize your creatives based on these events.

You can use the following method to pass event information to us::

- (void)logEvent:(NSString *)name withParameters:(NSDictionary *)parameters;

Here is how you set up some of the popular events.

**Registration Completed**

This event does not have any parameters::

 [[QGSdk getSharedInstance] logEvent:@"registration_completed" withParameters:nil];


**Category Viewed**

This event has one paraemter::

    NSMutableDictionary *categoryDetails = [[NSMutableDictionary alloc] init];
    [CategoryDetails setObject:@"apparels" forKey: @"category"];
                                   
    [[QGSdk getSharedInstance] logEvent:@"category_viewed" withParameters:categoryDetails];

**Product Viewed**

You may choose to have the following fields::
    
   NSMutableDictionary *productDetails = [[NSMutableDictionary alloc] init];
   [productDetails setObject:@"123" forKey:@"id"];                                      
   [productDetails setObject:@"Nikon Camera" forKey:@"name"];
   [productDetails setObject:@"http://mysite.com/products/123.png" forKey:@"image_url"];
   [productDetails setObject:@"myapp//products?id=123" forKey:@"deep_link"];
   [productDetails setObject:@"black" forKey:@"color"];
   [productDetails setObject:@"electronics" forKey:@"category"];
   [productDetails setObject:@"small" forKey:@"size"];
   [productDetails setObject:@"6999" forKey:@"price"];
   [[QGSdk getSharedInstance] logEvent:@"product_viewed" withParameters:productDetails];

**Product Added to Wishlist**::
    
    NSMutableDictionary *productDetails = [[NSMutableDictionary alloc] init];
    [productDetails setObject:@"123" forKey:@"id"];                                      
    [productDetails setObject:@"Nikon Camera" forKey:@"name"];
    [productDetails setObject:@"http://mysite.com/products/123.png" forKey:@"image_url"];
    [productDetails setObject:@"myapp//products?id=123" forKey:@"deep_link"];
    [productDetails setObject:@"black" forKey:@"color"];
    [productDetails setObject:@"electronics" forKey:@"category"];
    [prdouctDetails setObject:@"Nikon" forKey:@"brand"];
    [productDetails setObject:@"small" forKey:@"size"];
    [productDetails setObject:@"6999" forKey:@"price"];
    [[QGSdk getSharedInstance] logEvent:@"product_added_to_wishlist" withParameters:productDetails];

**Product Purchased**::
    
    NSMutableDictionary *productDetails = [[NSMutableDictionary alloc] init];
    [productDetails setObject:@"123" forKey:@"id"];                                      
    [productDetails setObject:@"Nikon Camera" forKey:@"name"];
    [productDetails setObject:@"http://mysite.com/products/123.png" forKey:@"image_url"];
    [productDetails setObject:@"myapp//products?id=123" forKey:@"deep_link"];
    [productDetails setObject:@"black" forKey:@"color"];
    [productDetails setObject:@"electronics" forKey:@"category"];
    [productDetails setObject:@"small" forKey:@"size"];
    [productDetails setObject:@"6999" forKey:@"price"];

and then::

    [[QGSdk getSharedInstance] logEvent:@"product_purchased" withParameters:productDetails];

or::

    [[QGSdk getSharedInstance] logEvent:@"product_purchased" withParameters:productDetails withValueToSum price];

**Checkout Initiated**::

    NSMutableDictionary *checkoutDetails = [[NSMutableDictionary alloc] init];
    [checkoutDetails setObject:@"2" forKey:@"num_products"];                                      
    [checkoutDetails setObject:@"12998.44" forKey:@"cart_value"];
    [checkoutDetails setObject:@"myapp://myapp/cart" forKey:@"deep_link"];
    [[QGSdk getSharedInstance] logEvent:@"checkout_initiated" withParameters:checkoutDetails];


**Product Rated**::
    
    NSMutableDictionary *productRated = [[NSMutableDictionary alloc] init];
    
    [productRated setObject:@"1232" forKey:@"id"];                                      
    [productRated setObject:@"2" forKey:@"rating"];
    [[QGSdk getSharedInstance] logEvent:@"product_rated" withParameters:productRated];

**Searched**::

     NSMutableDictionary *searchDetails = [[NSMutableDictionary alloc] init];
     [searchDetails setObject:@"1232" forKey:@"id"];                                      
     [searchDetails setObject:@"Nikon Camera" forKey:@"name"];
     [[QGSdk getSharedInstance] logEvent:@"searched" withParameters:searched];


**Reached Level**::
    
     NSMutableDictionary *level = [[NSMutableDictionary alloc] init];
     [level setObject:@"23" forKey:@"level"];                                      
     [[QGSdk getSharedInstance] logEvent:@"level" withParameters:level];


**Your custom events**

Apart from above predefined events, you can create your own custom events, and
have custom parameters in them::
    
    NSMutableDictionary *event = [[NSMutableDictionary alloc] init];
    [event setObject:@"2" forKey:@"num_products"];                                      
    [event setObject:@"some_value" forKey:@"my_param"];
    [event setObject:@"123" forKey:@"some_other_param"];
    [[QGSdk getSharedInstance] logEvent:@"my_custom_event" withParameters:event];


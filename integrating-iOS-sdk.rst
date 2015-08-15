iOS SDK integration
===================
Installing iOS SDK Library
--------------------------

Using Cocoapods
###############

The easiest way to integrate quantumgraph iOS SDK into your iOS project is to use CocoaPods.

#. Install CocoaPods using ``gem install Cocoapods``

#. If you are using Cocoapods for the first time, run ``pod setup`` to create a local CocoaPods spec mirror.

#. Create a file named ``Podfile`` in your Xcode project directory and add the following line in it.::
     pod 'quantumgraph'
#. Run ``pod install`` in Xcode project directory. Cocoapods will downloads and install the quantumgraph iOS-SDK library and create a new Xcode workspace. From now on use should use this workspace.

Manual Integration
###################

You can download the SDK from
   http://app.qgraph.io/static/sdk/ios/qgiossdk.tar.gz

#. In your Xcode project, Go to File, add new Group to your project and name it as QGSdk.

#. Add libQSdk.a and QGSdk.h in QGSdk group 

Generating PEM file
-------------------

To enable push notification in app

#. You need a PEM file - to send push notification to the app. 

#. You need to be signed with a provisioning profile that is configured for push.

Follow these steps to generate PEM file

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

#. Open Keychain Access on your Mac and choose the menu option Request a Certificate from a Certificate Authority
#. Enter some descriptive name for Common Name 
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
In your iOS app, Go to File, add new Group to your app and name it as QGSdk.

Add libQSdk.a and QGSdk.h in QGSdk group 

Registering for Remote Notification
###################################

In ``didFinishLaunchingWithOptions`` method of Appdelegate, add the following code for registering for remote notification::

   - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    UIUserNotificationType allNotificationTypes =
    (UIUserNotificationTypeSound | UIUserNotificationTypeAlert | UIUserNotificationTypeBadge);
    UIUserNotificationSettings *settings =
    [UIUserNotificationSettings settingsForTypes:allNotificationTypes categories:nil];
    [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    [[UIApplication sharedApplication] registerForRemoteNotifications];
    [[QGSdk getSharedInstance] onStart:@"your_app_name"];
    [[QGSdk getSharedInstance] logEvent:@"app_launched" withParameters:nil];    
    return YES;
    }

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

Logging user profile information
################################

User profiles are information about your users, like their name, city, date of birth or any other information that you may wish to track. You log user profiles by using one or more of the following functions::
     
    - (void)setUserId:(NSString \*)userId;

Ohter methods you may use to pass user profile prameters to us::

    - (void)setUserId:(NSString \*)userId;
    - (void)setName:(NSString \*)name;
    - (void)setFirstName:(NSString \*)name;
    - (void)setLastName:(NSString \*)name;
    - (void)setCity:(NSString \*)city;
    - (void)setEmail:(NSString \*)email;
    - (void)setDayOfBirth:(NSNumber \*)day;
    - (void)setMonthOfBirth:(NSNumber \*)month;
    - (void)setYearOfBirth:(NSNumber \*)year;

Other than these method, you can log your own custom user parameters. You do it using::

    - (void)setCustomKey:(NSString \*)key withValue:(id)value;

For example, you may wish to have the user's current rating like this::

    [[QGSdk getSharedInstance] setCustomKey:@"current rating" withValue:@"123"];


Logging events information
#########################
Events are the activities that a user performs in your app, for example, view the products, playing a game or listening to a music. Each event has a name (for instance, the event of viewing a product is called ``product_viewed``), and can have some parameters. For instance, 
for event ``product_viewed``, the parameters are ``id`` (the id of the product viewed), ``name`` (name of the product viewed), ``image_url`` (image url of the product viewed), ``deep_link`` (a deep link which takes one to the product page in the app), and so on.

It is not necessary that you provide all the parameters for a given event. You can choose to provide whatever parameters are relevant to you.

Once you log event information to use, you can segment users on the basis of the events (For example, you can create a segment consisting of users have not launched for past 7 days, or you can create a segment consiting of users who, in last 7 days, have purchased a product whose value is more than $1000)

You can also define your events, and your own parameters for any event. However, if you do that, you will need to sync up with us to be able to segment the users on the basis of these events or customize your creatives based on these events.

You can use the following method to pass event information to us::

- (void)logEvent:(NSString \*)name withParameters:(NSDictionary \*)parameters;

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
    [[QGSdk getSharedInstance] logEvent:@"product_purchased" withParameters:productDetails];

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


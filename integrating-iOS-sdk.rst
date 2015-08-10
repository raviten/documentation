iOS SDK integration
=======================

To enable push notification in app
---------------------------------

#. You need a PEM file - to send push notification to the app. 

#. You need to be signed with a provisioning profile that is configured for push.

Generating PEM file
-------------------
Follow these steps to generate PEM file

Generating App ID and SSL Certificate.
#####################################

#. Log in to the iOS Dev Center and select the “Certificates, Identifiers and Profiles” form the right panel
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
##########################################

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

Using iOS Sdk
-------------
In your iOS app, Go to File, add new Group to your app and name it as QGSdk.
Add libQSdk.a and QGSdk.h in QGSdk group 

**In your AppDelegate.m**

Make changes in the method didFinishLaunchingWithOptions::

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
    (UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    UserNotificationSettings *settings =
    [UIUserNotificationSettings settingsForTypes:allNotificationTypes categories:nil];
    [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    [[UIApplication sharedApplication] registerForRemoteNotifications];
    [[QGSdk getSharedInstance] onStart:@"<app_name> ];
    [[QGSdk getSharedInstance] logEvent:@"app_launched" withParameters:nil];
    return YES;
    }

Just build and run the app to make sure that you receive a message that app would like to send push notification. If you get code signing error, make sure that proper provisioning profile is selected

Add the following code in Appdelegate.m to get the device token for the user::

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
            NSLog(@"My token is: %@", deviceToken);
            [[QGSdk getSharedInstance] setToken:deviceToken]; t
    }

    - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
    {
     	    NSLog(@"Failed to get token, error: %@", error);
    }

QGSdk setToken method will log user’s token so that you can send push notification to the user.

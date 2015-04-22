Using QuantumGraph SDK
======================

You have installed QuantumGraph SDK using the previous doc, and now you are ready to send us various details about the user.

Firstly, we need to know some book keeping details:

#. Firstly, you need to import QGLogger and QGUtils classes in any file where you are calling SDK function::

      import com.quantumgraph.sdk.logger.QGLogger;
      import com.quantumgraph.sdk.logger.QGUtils;

   Declare a static class variable called “logger”::

      static QGLogger logger;

#. In onCreate() implementation of your Main activity must be called on the application starts, have the following code::

      //context must be of activity context
      QGLogger.activateApp(context);
      logger = QGLogger.newLogger(context);


#. We need to know the GCM Id of the user.
   For that, first you need to know your app’s “Sender ID” from google play store. See how to get sender id here: http://developer.android.com/google/gcm/gs.html#create-proj
   
   Once you know sender id, we provide a method using which you can get the GCM ID of that particular user::

      QGUtils.generateGcmId(Context context,String SenderId); 
   
   Note that you above method makes a call to google servers so you must call it in an async task to avoid blocking the UI thread.
   
   Once you have generated the GCM ID (by using above method or by yourself), you communicate to QG servers the GCM ID of the user by this method::
   
      logger.logGCMID(gcmid);

#. Nextly, there are two kinds of data about the user that you can pass to us:

   (a) User profile data: 
   This tells us about the attributes of the user. You can create your custom attributes to suit your need, by calling the function 
   
   ::

      logger.logProfileInfo(String customKey, String customValue);

   For instance, you write::
   
      logger.logProfileInfo(“first_name”, “Kiran”);
      logger.logProfileInfo(“last_name”, “Kumar”);
      logger.logProfileInfo(“city”, “Bangalore”);
      logger.logProfileInfo(“marital_status”, “single”);
   
   Once you tell us this data, you’ll be able to do two things:

   (a) Send messages to uses who satisfy a particular criterion: example, send message  to all the users from Bangalore
   (b) Personalize the message sent to a particular user. E.g. message to Mr. Kiran Kumar could start with: “Hi Kiran…”
   
   (b) Events data:
   This data tells us about various activities done by the user, for instance, searching, browsing the products, adding them to cart, rating the product and purchasing the product.
   
   Along with sending us the event name, you also send us some parameters of the event.
   
   Here is an example of sending an event, product_viewed::
   
      Bundle b = new Bundle();
      b.putString("name", "Shoe-Adidas-Sporty");
      b.putInt("price", 499);
      b.putInt(“image_url”, “http://url-to-image-of-show.com”);
      b.putInt(“deep_link”, “app://deeplink-to-show-page-in-app”);
      logger.logEvent("product_viewed",b);
 
   Once you have given this data to us, you can run a campaign which sends notification to people who have browsed a product within last few days and send them a message along with the image of the product that they browsed and clicking on the notification would open the product page (using the deeplink provided).
   

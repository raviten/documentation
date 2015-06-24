Integrating QGraph SDK
============================

Integration in Android Studio App
---------------------------------

#. Download QG.aar from
   http://app.qgraph.io/static/sdk/android/QG.aar.

   Put it in /libs folder of your app. Make a /libs folder if it does not already exist.

   .. image:: libs.png

#. Right click on project name and click on "Open Module Settings"

   .. image:: open-module-settings.png

#. Click on "+" button at the top left corner, select "Import .AAR package" option and navigate to QG.aar file in /libs folder of your app.

   .. image:: add-aar.png

#. In the same window, select your app in "Modules" sections from left, select "Dependecies" tab in top menu and click "+" button at bottom left. Select "Module dependency" and add "QG" as dependency. Select "OK" once that is done.

   .. image:: add-aar-to-project.png
   
Using Android SDK
-----------------
Follow these steps to use our android SDK

Import QG SDK in your activity
##############################
In all the activity classes, starting with the class for the main activity, import QG SDK::

   import com.quantumgraph.sdk.QG;

Initialization and cleanup of SDK
#################################
In the ``onStart()`` function of your activity, do the following::

   QG qg = QG.getInstance();
   qg.onStart(getApplicationContext(), <your app id>, <your sender id>);

App id for your app is available from the settings page. Sender id is a string
that Google provides to you for getting registration id for users. You will
get the sender id for your app during the set up phase in our web interface.

In the ``onStop()`` function of your activity, do the following::

   QG qg = QG.getInstance();
   qg.onStop();

Logging user profiles
#####################
User profiles are information about your users, like their name, city, date of birth
or any other information that you may wish to track. You log user profiles by using one
of the following functions::

   qg.setUserName(


Web SDK integration
===================

Background and Terminology
--------------------------
For easier integration, first let us understand some fundamentals. This description holds true
for Google Chrome and Firefox browsers. Fundamentals are the same for Safari, though the details
differ.

Browsers support webpush only for HTTPS sites. We provide a workaround if you have HTTP site
by providing you with a backing HTTPS site.

We give you two files: qg-service-worker.js and manifest.json and a snippet of javascript code.
You install the files in the root folder of your web server and put javascript code in various
web pages of your website. Our tag downloads some more javascript code from our servers.

When a user comes to your website, our code ask the browser to request the user to grant us
the permission to send her web push notifications. If the user agrees, the browser returns
us a token (you can think of it like an address of the browser) which the code transfer to 
the QuantumGraph servers. Using this token, QuantumGraph servers can send the web push 
notification to users.

Our SDK (which gets downloaded from the javascript code that we provide you) also communicates
to QuantumGraph servers the URLs which are you accessing. As a result, you can, using our 
web panel, send a push to the users who have browsed a particular URL. Using our SDK, you
can also send us other attributes of the user (such as user id, email, city) and then send
notifications based on those attributes.

To better understand the documentation that follows, it is useful to understand some terminology
used later.

**System Prompt**

System prompt is that prompt that appears when the browser requests the user to grant a certain
website permission to show her webpush. It looks like this:

   .. figure:: images/web-push-system-prompt.png
      :align: center

If the user clicks "Allow" then the browser gives a device token to the QG SDK. If the user clicks
"Block" then the browsers does not give the device token to the QG SDK.

**Fake prompt**

If you display a system prompt to the user and she blocks your website, then the browser does not
allow you to display the system prompt again (unless the user explicity modifies the notification
settings by herself). Thus, before displaying the system prompt, it may be good to ask for a 
"pre approval", by display a fake prompt. If the user allows the notifications in the fake prompt,
only then you display the system prompt. If the user disallows the notifications in the fake
prompt, you can show the fake prompt after some time (1 hr, 1 day, or more) and request the
user again. 

This is what a fake prompt looks like. You can customize the text as per your needs.

   .. figure:: images/web-push-fake-prompt.png
      :align: center

**Overlay**

When displaying the system prompt, you may wish to deemphasize the content of your website,
to direct the user attention to the system prompt. This may be done by an overlay screen, which
looks like this. You can customize the text as per your needs.

   .. figure:: images/web-push-overlay.png
      :align: center

**Thank you prompt**

You can display a thank you message once the user has granted permission to send
the web push. You can customize this message, and change its location. It looks like this.

   .. figure:: images/web-push-thank-you.png
      :align: center


Installing Web Pixel
--------------------
Here we describe how you can integrate QGraph's pixel on your website. 

If your site is HTTPS
#####################
#. Login to http://app.qgraph.io

#. Go to "Setup" -> "Integrations" section on the side bar menu. Select "Web" from the three channels presented to you.

#. Enter your website's URL and follow the instructions provided.

#. Download the following files

   #. https://s3-ap-southeast-1.amazonaws.com/qgraph-web-push/qg-service-worker.js 
   #. https://s3-ap-southeast-1.amazonaws.com/qgraph-web-push/manifest.json

   Copy above files to the root folder of your website. In case you want to use your GCM key, put your sender id in ``manifest.json`` above instead of the sender is already present in the file. In this case you also need to enter your GCM key during the intergration. (We need this GCM key to send web push notifications)

   Add the following lines inside of head or body tag::

    <script type="text/javascript">
       window.QGSettings = {
          "appId": "<your app id>",
          "push": {
             "fakePrompt": true,
             "prompt": {
                "title": "Get Latest Updates",
                "message": "Subscribe to notifications"
             }
          }
       };
        !function(q,g,r,a,p,h){if(q.qg)return;n=q.qg=function(){n.callmethod?n.callmethod.call(n,arguments):n.queue.push(arguments);};n.queue=[];p=g.createElement(r);p.async=!0;p.src=a;h=g.getElementsByTagName(r)[0];h.parentNode.insertBefore(p,h)}(window,document,'script','https://cdn.qgraph.io/dist/qgraph.min.js');
        qg('init',window.QGSettings);
    </script>

   In the above code, if the user will be shown a "fake prompt" if you set the variable ``fakePrompt`` to ``true``. The text of the prompt will be derived from the fields in ``prompt``.

   There are several more parameters which you can tweak to set the behavior of the pixels. Here is a code snippet which uses all the allowed parameters::

    <script type="text/javascript">
        window.QGSettings = {
        "appId": "<your app id>",
        "push": {
           "popupAlignment": "center" or "top left" or "top right" or "bottom left" or "bottom right",
           "secondsBetweenPrompts": 3600, 
           "delay": 1 , 
           "requestSelf": false,
           "fakePrompt": true,
           "prompt": {
              "title": "Fake prompt",
              "message": "Lorem opsem and so on"
           },
           "overlay": { 
              "title": "Allow us to send you notifications",
              "message": "We will not send you spam notifications"
           },
           "thankYouPrompt": {
             "title": "Thanks for subscribing!",
             "message": "Happy Browsing",
           }
        },
    };
    </script>


Some explanation is in order:

*popupAlignment*
Controls the alignment of the fake prompt, if it is requested. Default value is ``center``. Other possible values are ``top left``, ``top right``, ``bottom left``, ``bottom right``.

*delay*:
Controls how many seconds after the user has come on the website, do we show system prompt/fake prompt as applicable. Default value is 0.

*secondsBetweenPrompts*:
Controls the number of seconds between two fake prompts, in case the user declines the request for notification on the first fake prompt. Default value is 3600.

*requestSelf*:
If set to ``true``, the QGraph SDK does not show the system prompt or the fake prompt by itself. It is your code which decides when to do that. You can show the prompt any time by this code::

    qg("prompt-push")

Note that if you use this parameter, *delalay* and *secondsBetweenPrompts* are not considered.
Default value is ``false``.

*prompt*:
Controls the content of the fake prompt, in case ``fakePrompt`` is ``true``.

*overlay*:
If set, an overlay will be shown while system prompt is displayed.

*thankYouPrompt*::
If set, a thank you message will be shown when the user grants permission for web push.

If your site is HTTP
####################

In case your site is HTTP, you need a backing HTTPS site to enable push notifications. QGraph provides a backing HTTPS site. If you would rather use your own backing HTTPS site, please contact app@qgraph.io for instructions. 

#. Login to http://app.qgraph.io

#. Go to "Setup" -> "Integrations" section on the side bar menu. Select "Web" from the three channels presented to you.

#. Enter your website's URL and follow the instructions provided.

#. Download the following files

   #. https://s3-ap-southeast-1.amazonaws.com/qgraph-web-push/qg-service-worker.js 
   #. https://s3-ap-southeast-1.amazonaws.com/qgraph-web-push/manifest.json

   Copy above files to the root folder of your website.  Enter an ``endpoint`` in the web panel for web push integration. Keep the endpoint similar to the name of your website. For example, if the name of your company is XYZ ECommerce, then `xyz` may be a good endpoint to use. The web push notification will be delivered for the domain `xyz.qgr.ph`.

In this case, basic pixel to use is::

    <script type="text/javascript">
        window.QGSettings = {
            "appId": "<your app id>",
            "qgendpoint": "<your end point>",
            "push": {
              "requestSelf": false,
              "fakePrompt": false,
              "prompt": {
                 "title": "Get Latest Updates",
                 "message": "Subscribe to notifications"
              }
           }
        };
        !function(q,g,r,a,p,h){if(q.qg)return;n=q.qg=function(){n.callmethod?n.callmethod.call(n,arguments):n.queue.push(arguments);};n.queue=[];p=g.createElement(r);p.async=!0;p.src=a;h=g.getElementsByTagName(r)[0];h.parentNode.insertBefore(p,h)}(window,document,'script','https://cdn.qgraph.io/dist/qgraph.min.js');
        qg('init',window.QGSettings);
    </script>


Here is an advanced pixel with various options::

    <script type="text/javascript">
       window.QGSettings = {
          "appId": "<your app id>",
          "qgendpoint": "<your end point>",
          "push": {
             "popupAlignment": "center" or "top left" or "top right" or "bottom left" or "bottom right",
             "secondsBetweenPrompts": 3600, 
             "delay": 1 , 
             "fakePrompt": true,
          },
          "prompt": {
             "title": "Fake prompt",
             "message": "Lorem opsem and so on"
          },
          "overlay": { 
             "title": "Allow us to send you notifications",
             "message": "We will not send you spam notifications"
          },
          "thankYouPrompt": { 
             "title": "Thanks for subscribing!",
             "message": "Happy Browsing",
          }
       };
    </script>


Logging Data
------------
QG web SDK provides you ways to send us data about the users. Once you send us the data you can segment on the basis of that data (E.g. send a web push to users meeting certain criterion) and customize on the basis of that data (E.g. insert the image of the product that the user has seen, or the image of the product that you recommend for the user). You can send us two types of data: the attributes of a user, like email, name, city etc. (what we call profile information) and the data related to the activity that the user is doing.

Logging profile information
###########################

You log profile information using `identify` functionality of the function ``qg``. For instance::

   qg("identify", {"email": "myemail@somedomain.com"});

logs the email of the user. You can set multiple properties at once, like this::

   qg("identify", {"email": "myemail@somedomain.com", "first_name": "John", "last_name": "Doe"});


Logging event information
#########################

You log events using `event` functionality of the function ``qg``. Following code logs an event `product_viewed`::

   qg("event", "product_viewed");

You can have parameters related to the events. For example, following code logs an event `product_viewed` with parameters product_id, name and price::

   qg("event", "product_viewed", {"product_id": 123, "name": "Adidas shoes", "price": 4000});

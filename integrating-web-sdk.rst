Web SDK integration
===================

Installing Web Pixel
--------------------
Integration instructions are different depending on whether your site is HTTPS or HTTP.
Browsers support web push only for HTTPS websites. Thus integration is relatively straightforward if your website is HTTPS. In case your site is HTTP, we provide a backing HTTP site to support web push.

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
             "requestSelf": false,
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

   In the above code, if you will shown a "fake prompt" if you set the variable ``fakePrompt`` to ``true``. Fake prompt is a prompt which appears on the website before the actual, system prompt apears. The advantage of showing fake prompt first is that in case the user decides not to subscribe to your notification now, you have more chances to request the user again. However, if the user declines to receive notifications on a system prompt, no further request is possible.

   There are several more parameters which you tweak to set the behavior of the pixels. Here is a code snippet which uses all the allowed parameters::

    <script type="text/javascript">
        window.QGSettings = {
        "appId": "<your app id>",
        "push": {
           "popupAlignment": "center" or "top left" or "top right" or "bottom left" or "bottom right",
           "secondsBetweenPrompts": 3600, /* If a user declines the web push permission on a fake prompt, how many seconds more to wait */
           "delay": 1 , /* How many seconds to wait on a page before displaying fake prompt */
           "fakePrompt": true,
              "prompt": {
              "title": "Fake prompt",
              "message": "Lorem opsem and so on"
           },
           "overlay": { /* Message displayed while showing system prompt */
              "title": "Allow us to send you notifications",
              "message": "We will not send you spam notifications"
           },
           "thankYouPrompt": { /* This is an optional section */
             "title": "Thanks for subscribing!",
             "message": "Happy Browsing",
           }
        },
    };
    </script>


If your site is HTTP
####################

In case your site is HTTP, you need a backing HTTPS site to enable push notifications. If you would like to use your own backing HTTPS site, please contact app@qgraph.io for instructions. If you would like us to provided backing HTTPS site, enter an ``endpoint`` in the web panel for web push integration. Keep the endpoint similar to your name. For example, if the name of your company is XYZ ECommere, then `xyz` may be a good endpoint to use.
The web push notification will be delivered for the domain `xyz.qgr.ph`.

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
             "secondsBetweenPrompts": 3600, /* If a user declines the web push permission on a fake prompt, how many seconds more to wait */
             "delay": 1 , /* How many seconds to wait on a page before displaying fake prompt */
             "fakePrompt": true,
          },
          "prompt": {
             "title": "Fake prompt",
             "message": "Lorem opsem and so on"
          },
          "overlay": { /* Message displayed while showing system prompt */
             "title": "Allow us to send you notifications",
             "message": "We will not send you spam notifications"
          },
          "thankYouPrompt": { /* This is an optional section */
             "title": "Thanks for subscribing!",
             "message": "Happy Browsing",
          }
       };
    </script>


Logging Data
------------
QG web SDK provides you ways to send us data about the users. You can send us two types of data: the attributes of a user, like email, name, city etc. (what we call profile information) and the data related to the activity that the user is doing.

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

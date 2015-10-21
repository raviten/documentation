Using API
=========
We provide programmatic access to several features of our platform.

1. Log in on https://app.qgraph.io and click your name on the top right
of the screen. In the drop down menu that comes, select "Account Settings".
Note down the "API Token" for your account.

2. You need to set Authorization key as ``"Token: <your API token>"`` in the header of your http requests.
For instance, if using curl, you do it like this::

   curl -H "Authorization: Token abcd" https://app.qgraph.io/api/<some endpoint>

Sending notifications
---------------------
First you need to create a campaign. Go to https://app.qgraph.io, log in, go to "Campaigns" tab and create a new campaign. You can fill any value in the campaign, they will be overridden by what you provide in the api call.

Once you have created the campaign, click on the campaign name to go its performance page. URL of the performance page looks like: ``https://app.qgraph.io/campaign/<campaign id>/performance``. Note down the campaign id for your campaign.

Next you can make a HTTP POST request at ``https://app.qgraph.io/api/send-notification``. The POST body can be one of the two formats

Using registration id of the users
##################################
In case you know the registration id of the users, you can use the following POST body::

   {
      "cid": <campaign id>,
      "registration_ids": ["registrationid 1", "registrationid 2", ..., "registrationid n" (upto 1000 registration ids)]
      "message": {
         "title": <title of the notification>
         "message: <body of the notification>
         "imageUrl": <url of the icon image> (optional)
         "bigImageUrl": <url of the big image> (optional)
         "deepLink": <deep link of notification> (optional)
      }
   }


Using user ids of the users
###########################
If you do not know the registration id of the users, you should have set their user id's by calling the function ``setUserId()`` in your mobile app. Once you do that, you can use these user ids to send notifications, as follows::

   {
      "cid": <campaign id>,
      "user_ids": ["userid 1", "userid 2", ..., "userid n" (upto 1000 user ids)]
      "message": {
         "title": <title of the notification>
         "message: <body of the notification>
         "imageUrl": <url of the icon image> (optional)
         "bigImageUrl": <url of the big image> (optional)
         "deepLink": <deep link of notification> (optional)
      }
   }

Getting user profiles
---------------------
2. Send a GET request to https://app.qgraph.io/api/get-user-profiles/. For instance, if your token is ``abcd``, the relevant call in curl would be::

    curl -H "Authorization: Token abcd" https://app.qgraph.io/api/get-user-profiles/


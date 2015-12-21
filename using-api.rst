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

Next you can make a HTTP POST request at ``https://app.qgraph.io/api/send-notification``. The POST body is of the following format::

   {
      "cid": <campaign id>,

      "registration_ids": ["regd 1", "regid 2", ..., "regid n" (upto 500 registration ids)]
      or
      "user_ids": ["userid 1", "userid 2", ..., "userid n" (upto 500 userid)]
      or
      "emails": ["email 1", "email 2", ..., "email n" (upto 500 emails)]
      
      "message": <message in the format described below>
   }


You need to provide either ``registration_ids`` or ``user_ids`` or ``emails``

For a simple notification, ``message`` is of the following format::

   {
       "title": <title of the notification>,
       "message: <body of the notification>,
       "imageUrl": <url of the icon image> (optional),
       "bigImageUrl": <url of the big image> (optional),
       "deepLink": <deep link of notification> (optional)
   }

For banner notification, ``message`` is of the following format::

   {
       "title": <title of the notification>,
       "message: <body of the notification>,
       "contentImageUrl": <url of banner image>,
       "deepLink": <deep link of notification> (optional)
   }

For animated banner notification, ``message`` is of the following format::

   {
       "title": <title of the notification>,
       "message: <body of the notification>,
       "deepLink": <deep link of notification> (optional)
       "type": "animation"
       "animation": {
           "millisecondsToRefresh": <duration between two frames in milliseconds>,
           "images": [url1, url2, ..., url n]
       }
   }

Getting user profiles
---------------------
Send a GET request to https://app.qgraph.io/api/get-user-profiles/. For instance, if your token is ``abcd``, the relevant call in curl would be::

    curl -H "Authorization: Token abcd" https://app.qgraph.io/api/get-user-profiles/

You can optionally provide parameters ``start_date`` and ``end_date`` to the API call. If these parameters are provided, the API fetches
entries only for the users who have installed the app on or after ``start_date``, but on or before ``end_date``. The format of the both the 
arguments is ``yyyy-mm-dd``. A sample call would be::

    curl -H "Authorization: Token abcd" https://app.qgraph.io/api/get-user-profiles/?start_date=2015-12-22&end_date=2015-12-25


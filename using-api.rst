Using API
=========
We provide programmatic access to several features of our platform.

1. Log in on https://app.qgraph.io and click your name on the top right
of the screen. In the drop down menu that comes, select "Account Settings".
Note down the "API Token" for your account.

2. You need to set Authorization key as ``"Token: <your API token>"`` in the header of your http requests.
For instance, if using curl, you do it like this::

   curl -H "Authorization: Token abcd" <relevant api url>

Sending notifications
---------------------
First you need to create a campaign. Go to https://app.qgraph.io, log in, go to "Campaigns" tab and create a new campaign. You can fill any value in the campaign, they will be overridden by what you provide in the api call.

Once you have created the campaign, proceed to edit that campaign. URL of the performance page looks like: ``https://app.qgraph.io/#/edit_campaign/<campaign id>``. Note down the campaign id for your campaign.

Next you can make a HTTP POST request at ``https://api.qgraph.io/api/v2/send-notification/``. The POST body is of the following format::

   {
      "cid": <campaign id>,

      "registration_ids": ["regd 1", "regid 2", ..., "regid n" (upto 500 registration ids)]
      or
      "user_ids": ["userid 1", "userid 2", ..., "userid n" (upto 500 userid)]
      or
      "emails": ["email 1", "email 2", ..., "email n" (upto 500 emails)]
      or
      "segment_id": <segment id>
      
      "message": <message in the format described below>
      "os": "android" or "ios-dev" or "ios-prod"
   }

You need to provide one of ``registration_ids``,  ``user_ids``, ``emails`` or ``segment_id``. In case you provide segment id, specified segment id must be a valid segment in your account, and notifications will go to that segment. To find segment id of a given segment, proceed to edit that segment. URL of the segment page is of the format ``https://app.qgraph.io/#/edit_segment/<segment id>``.

If you want to send notification to android devices, use ``android`` for key ``os``. If you want to send notification to ios devices, use ``ios-dev`` or ``ios-prod``, depending on whether you want us to use development profile or production profile. (You should have uploaded the respective .pem file to us)

For a simple android notification, ``message`` is of the following format::

   {
       "type": "basic",
       "title": <title of the notification>,
       "message": <body of the notification>,
       "imageUrl": <url of the icon image> (optional),
       "bigImageUrl": <url of the big image> (optional),
       "deepLink": <deep link of notification> (optional)
       "actions": [{"id": 1, "text": "<button 1 text>", "deepLink": "<deep link if any>"}, 
                   {"id": 2, "text": "<button 2 text>", "deepLink": "<deep link if any>"}, 
                   {"id": 3, "text": "<button 3 text>", "deepLink": "<deep link if any>"}] <optional>
   }


A note about action buttons:
If you would like a campaign with action buttons to be a poll campaign (where, on button press, response of the user is recorded, but the app does not open), set the key `poll` to `true` in the `message`. You can send 1, 2 or 3 actions, and deep link within each button is optional.

For ios notification, ``message`` is of the following format::

   { 
        "aps": {
           "alert": {
               "title": <title of the notification>,
               "body": <body of the notification>
            }
         },
         "deepLink": <deep link> (optional)
   }

For banner notification (available only in android), ``message`` is of the following format::

   {
       "type": "banner",
       "title": <title of the notification>,
       "message": <body of the notification>,
       "contentImageUrl": <url of banner image>,
       "deepLink": <deep link of notification> (optional)
   }

For carousel notification (available only in android), ``message`` is of the following format::

   {
       "type": "carousel",
       "title": <title of the notification>,
       "message": <body of the notification>,
       "deepLink": <deep link of notification> (optional),
       "qg_prev_button":"https://cdn.qgraph.io/img/left.png",
       "qg_next_button":"https://cdn.qgraph.io/img/right.png",
       "carousel": [{"image": "<URL of the image>", 
                     "deepLink": "<deep link for image, if any>", 
                     "title": "<title of image (optional)>", 
                     "message": "<message of image (optional)>"}, 
               ... you can have up to 10 such elements]
   }


Note that you can customize previous and next buttons by using your own image URL.

For slider notification (available only in android), ``message`` is of the following format::

   {
       "type": "slider",
       "title": <title of the notification>,
       "message": <body of the notification>,
       "deepLink": <deep link of notification>, (optional)
       "qg_prev_button":"https://cdn.qgraph.io/img/left.png",
       "qg_next_button":"https://cdn.qgraph.io/img/right.png",
       "slider": [{"image": "<URL of the image>", 
                  "deepLink": "<deep link for image, if any>"}, 
               ... you can have up to 10 such elements]
   }

Note that you can customize previous and next buttons by using your own image URL.

For animated banner notification (available only in android), ``message`` is of the following format::

   {
       "title": <title of the notification>,
       "message": <body of the notification>,
       "deepLink": <deep link of notification>, (optional)
       "gifPlayButton": "https://cdn.qgraph.io/img/video_button.png",
       "type": "animation"
       "animation": {
           "millisecondsToRefresh": <duration between two frames in milliseconds>,
           "images": [url1, url2, ..., url n]
       }
   }


Specifying key value pairs
##########################
You can specify key value pairs in (both android and ios) notifications. To do this, include a key ``qgPayload``
in your ``message`` dictionary. ``qgPayload`` should contain key-value pairs. For example, a sample ``message`` for android
would be::

   {
       "type": "basic",
       "title": <title of the notification>,
       "message: <body of the notification>,
       "imageUrl": <url of the icon image> (optional),
       "bigImageUrl": <url of the big image> (optional),
       "deepLink": <deep link of notification> (optional)
       "qgPayload": {
           "key1": "some value",
           "key2": 123
        }
   }

Key value pairs can then be extracted in your activity as described here: http://docs.qgraph.io/en/latest/integrating-android-sdk.html#receiving-key-value-pairs-in-activity


Getting user profiles
---------------------
Send a GET request to https://app.qgraph.io/api/get-user-profiles/. For instance, if your token is ``abcd``, the relevant call in curl would be::

    curl -H "Authorization: Token abcd" https://app.qgraph.io/api/get-user-profiles/

Specifying start and end dates
##############################
You can optionally provide parameters ``start_date`` and ``end_date`` to the API call. If these parameters are provided, the API fetches
entries only for the users who have installed the app on or after ``start_date``, but on or before ``end_date``. The format of the both the 
arguments is ``yyyy-mm-dd``. A sample call would be::

    curl -H "Authorization: Token <your token>" https://app.qgraph.io/api/get-user-profiles/?start_date=2015-12-22&end_date=2015-12-25

For faster response times, you should retrieve the data for small date ranges.

Specifying OS
#############
You can specify the ios for which you want to retrieve data. You specify this by
providing a query parameter ``os`` whose values can be ``android`` (for android), ``ios-prod`` (for ios using production profile), or ``ios-dev``
(for ios using development profile). Default value for ``os`` is ``android``. Here is an example of using this variable::

    curl -H "Authorization: Token <your token>" https://app.qgraph.io/api/get-user-profiles/?start_date=2015-12-22&end_date=2015-12-25&os=android

Specifying specific fields to retrieve
######################################
You can get following fields using the api:

#. *firstSeen*: Date when the user installed your app
#. *mTime*: Latest date when the user accessed your app
#. *monthlyActivity*: Number of days in last 30 days when the user accessed your app
#. *email*: email of the user, if available
#. *qgCity*: city of the user, if available
#. *uninstallTime*: date when we detected that the user has uninstalled your app
#. *user_id*: the user id set by ``setUserId()`` function of the SDK
#. *qgType*: tells whether the install is a fresh one or a reinstall
#. *qgSrc*: source of the install, if available
#. *gcmId*: gcm registration id of the user in case of android and device token in case of ios
#. *deviceId*: device id of the user
#. *advId*: advertiser id of the user

You can specify what specific fields you want. For instance, if you want to get *firstSeen*, *uninstallTime* and *gcmId* of all the users who installed
your app between December 1, 2015 and December 3, 2015, the relevant curl call would be::

    curl -H "Authorization: Token <your token>" https://app.qgraph.io/api/get-user-profiles/?start_date=2015-12-01&end_date=2015-12-03&fields=firstSeen,uninstallTime,gcmId

For faster response times, you should retrieve only the fields that you need.

Create a user uploaded segment
------------------------------
You usually create a user uploaded segment by manually uploading a file in the Segment -> Add New -> Uploaded Segment. However, you can also do it using our API. Uploading a segment is a two step process:

First you need to upload the segment file. This file needs contain one field value (such as email) per line. You upload it by a command similar to this::

    curl -H 'Authorization: Token <your token>'\
         -H 'content-type: multipart/form-data'\
         -F file=@/path/to/your/file\
         https://app.qgraph.io/qganalyzedata/upload-segment-file/

This gives an output like::

    {"filename": "upload.csv1495733409.41"}

Here ``upload.csv1495733409.41`` is the temporary name of the file that has been created on the server. You will need this name in the second step.

Secondly, you use above outputted temporary filename to create a segment, like this::

    curl -X POST\
         -H 'Authorization: Token <your token>'\ 
         -H 'appId: <your app id>' \
         -H 'content-type: application/json'\
         -d '{"name": "<name of the segment>", "description": "<description of the segment>", "filename": "<filename produced in step 1>", "field": "<field name whose values are present in the file>"}'\
         https://app.qgraph.io/qganalyzedata/upload_segment/
       
For instance, if the uploade file contained email, a sample command to upload the file would be::

    curl -X POST\
         -H 'Authorization: Token <your token>'\
         -H 'appId: <your app id>' 
         -H 'content-type: application/json'\
         -d '{"name": "my uploaded segment", "description": "This is a bunch of emails", "filename": "upload.csv1495733409.41", "field": "email"}'\
         https://app.qgraph.io/qganalyzedata/upload_segment/

Segment is created as a result of this request.



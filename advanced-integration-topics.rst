Advanced Integration Topics
============================

Passing Dates and Times to QGraph Servers
-----------------------------------------
Sometimes you may wish to pass date or time to QGraph servers. For instances, you may wish to pass the
date of journey to QGraph servers. QGraph SDKs (iOS, android and web) take only integers, real numbers,
strings and booleans as parmeters. Thus, dates and times need to be formatted as strings before they
can be passed to QGraph. This section describes the format which is understood by QGraph servers.

Format for Date
###############
Format for date is ``YYYY-MM-DD``. Thus, July 12, 2017 would be passed as string ``"2017-07-12"``.

Format for Time
###############
Format for time is ``HH:MM:SS`` where HH is in 24 hour format. Thus, 3:10:06 PM is to be formatted as ``"15:10:06"`` and 2:04:42 AM
is to be formatted as ``"02:04:42"``.

Format for Datetime
###################
There are two formats for datetime:

#. Timezone unaware datetime is of the format ``YYYY-MM-DDTHH:MM:SS``. For example, ``"2017-07-12T15:10:06"``.

#. Timezone aware datetime format is ``YYYY-MM-DDTHH:MM:SS[+/-]HH:MM``. For example, ``"2017-07-12T15:10:06+05:30"`` is a datetime in IST timezone, while ``"2017-07-12T15:10:06-08:00"`` is a datetime in PST timezone.

Passing data to QGraph from your servers
----------------------------------------
The usual way for QGraph to get your users' data is through our SDKs. As your users'
interact with your android, iOS or web app, you call some functions of QGraph SDK
and transmit the activities to QGraph servers.

In some cases, you would need to pass your users' data to QGraph through your servers.
For example, if you are an ecommerce website, you may wish to pass QGraph an event 
when a customer order has been dispatched, or when an order has been delivered to
your customer. This is so that you may run a triggered campaign on, say, order 
delivery, sending a notification to your user when the order has been delivered.

You can pass us two kinds of information about your users: 

1. Profile information: 

   This is information like name, city, or birthday of the user.
2. Event information: 

   This is information about an event like an order has been picked up, or an order has been delivered.

To pass data to QGraph servers, you make an HTTP POST request to the following URL::

    https://api.quantumgraph.com/qga/clients-data/

Note that in one call, you can transfer the information about one user only.

POST body format is as following::

    {
       "appId": <your app id>
       "appSecret": <your app secret>,
       "identifier": <an identifier to identify the user>
       "identifier_value": <value of the identifier>,
       "device": <device type of the user>
       "profiles": <profile information of the user, if any>,
       "events": <even information, if any>
    }

Let us consider these fields one by one.

*appId* is the unique identifier for your account. It is available from "Account Settings" page of your account.

*appSecret* is the unique secret for your app. It is also available from "Account Settings" page of your account.

*identifier* is the attribute on the basis of which you identify the user. It can be one of the following: (a) *user_id*, as set by calling the function *setUserId()* from within the SDK (b) *email*, as set by calling the function *setEmail()* from within the SDK (c) *IDFA*, for iOS users, (d) *advertisingId*, for android users. Note that if you are identifying the users through *user_id* or *email*, you should have passed the *user_id* or *email* of the user by calling *setUserId()* or *setEmail()* from within the SDK.


*identifier_value* is the value of the *identifier* for the user for whom the request is being made.

*device* is one of *android*, *ios*, *web* or *fb*. 

*profiles* is an optional key. Its value is a dictionary consisting of key value paris that you want to set for the particular user. An example profile entry would be::

    {
        "first_name": "Jacob",
        "last_name": "John"
    }

*events* is an optional key. Its value is a list consisting of dictionaries. Each dictionary contains an *eventName* and an optional dictionary consiting *parameters* which in turn consists of parameter name and values. Here is an example consisting of a single event::

   [{
       "eventName": "user_registered"
   }]

And here is an example consisting of two events, one with a parameters and the other without parameters::

   [{
        "eventName": "user_registered"
    },
    {
        "eventName": "order_placed",
        "parameters": {
            "order_id": "3j3dkd4k50",
            "order_value":  253
        }
    }]


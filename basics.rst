Basics
------

How does it work?
=================

Here is a thousand feet overview of how QGraph SDK sends notifications to your app users.

#. You register at our site integrate the SDK in your app. SDK provides you with some functions that you can call to send us data related to your app users.  

#. You send us user data by calling the functions of the SDK. There are two types of data: user profile data, like the name of user, gender of user, city of user etc) and event (activity) data, like a user viewing a product, a user purchasing a product, etc.

#. You go to our web panel at http://app.qgraph.io. You create one more segments. A segment is a set of users. For instance you can create a segment of the users who reside in Bangalore and have not opened your app in last 7 days. You also create a campaign. A campaign is a segment together with a creative (i.e. the title, the message, the image etc of the notification). Once you have created a campaign, you send the notification.

#. After you send the notification, you can go to respective campaign and view statistics around how many of those notifications were opned, and what events happend as a result of notifications.

User Profiles and Events
========================
Nextly you need to know about user profiles and events.

User Profiles
#############
User profile is information regarding attributes of the user: for instance his name, email, city,
gender and so on. User profile may contain information that is specific to your app, for example, the maximum level attained in a gaming app, total lifetime purchase made by a user, or his interests. Each user profile item has a "key" and a "value". For instance for the name of a person, the key is "name" and value might be "John Appleseed".


Events
######
Events are the activities that a user performances, for instance viewind a product, purchasing a product, playing a game or liking an item. Each event has a name, say "product_viewed", or "product_purchased". Each event also has some parameters which consist of "keys" and "values". For instance, for the event "product_viewed", the parameter keys would be "id", "name", "img_url", "deep_link" etc with sample values 123, "Nikon Camera", "http://mysite/product/123.png" and "myapp://myapp/product/123" respectively.


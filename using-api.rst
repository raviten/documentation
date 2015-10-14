Using API
=========
We provide an API to download user profile information. To do this,
proceed as follows

1. Log in on http://app.qgraph.io and click your name on the top right
of the screen. In the drop down menu that comes, select "Account Settings".
Note down the "API Token" for your account.

2. Send a GET request to http://app.qgraph.io/api/get-user-profiles/ with ``"Token: <your API token>"``
as Authorization key in header of the request. For instance, if your API token is ``abcd``, using curl, you can do the following to get user profile information::

    curl -H "Authorization: Token abcd" http://app.qgraph.io/api/get-user-profiles/


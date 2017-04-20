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
####################
There are two formats for datetime:

#. Timezone unaware datetime is of the format ``YYYY-MM-DD HH:MM:SS``. For example, ``"2017-07-12 15:10:06"``.

#. Timezone aware datetime format is ``YYYY-MM-DD HH:MM:SS[+/-]HH:MM``. For example, ``"2017-07-12 15:10:06+05:30"`` is a datetime in IST timezone, while ``"2017-07-12 15:10:06-08:00"`` is a datetime in PST timezone.

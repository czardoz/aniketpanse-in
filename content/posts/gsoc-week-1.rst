Beeswarm - Week 1
=================

:date: 2013-06-25
:tags: beeswarm, gsoc, honeypots


As a part of GSoC 2013, I'm working on improving `BeeSwarm <https://github.com/honeynet/beeswarm>`_.
This is the first of my weekly updates (and probably the longest). It's been a pretty eventful
first week of GSoC. A lot of code was written, knowledge gained and planning
done! :) Here's a short summary:

Improving the SSH and Telnet capabilities:

* The Hive now accepts logins and allows users to run a bunch of common commands (ls, cat, cd, uname, etc) on the SSH and Telnet capabilities.
* A lot of code was refactored, so that implementation of new commands is easier, and common to both the capabilities.
* Quite a few minor bug-fixes, and the Telnet Bee was improved, so that it handled the options negotiations in the right way.

Improving the Web-App UI:

* The Beekeeper now boasts a new UI, built on Twitter Bootstrap.
* It is now possible (and necessary) to login to the web-app in order to perform
  administrative tasks and view the logs.
* The logs are displayed in a pretty table, and sorting and pagination is possible.
* BeeSwarm no longer depends upon Flask-Bootstrap.

This week, I plan to secure the BeeKeeper API. The Hive and Feeder should refuse
sending data in case an invalid certificate is detected. I have a feeling
implementing this will be much harder than it sounds.. looking forward to a busy week!

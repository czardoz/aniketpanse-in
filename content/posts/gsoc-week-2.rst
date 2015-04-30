Beeswarm - Week 2
=================

:date: 2013-07-05
:tags: beeswarm, gsoc, honeypots
:bg: /img/bgs/gsoc-2013.png

A pretty quiet week, this one (I moved to a new city). We now have a POP3s capability.
There are a few problems with the HTTPS capability, which results in SSL errors on
the major browsers. It has been decided to handle these a bit later.

The cool part is that I have started working on `Issue #89 <https://github.com/honeynet
/beeswarm/issues/89>`_. It'll require quite a few
changes to the core web-app, so I'll likely be very busy in the coming week. Also,
I need to decide on a suitable WSGI server, which will serve traffic over SSL and
which works with Gevent. I am currently considering ``Gunicorn`` or the bundled
``gevent.pywsgi.WSGIServer``. I guess I'll be properly occupied in the coming weeks.


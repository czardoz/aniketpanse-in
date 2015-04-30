Beeswarm Updates
================

:date: 2013-09-01
:tags: beeswarm, gsoc, honeypots
:bg: /img/bgs/gsoc-2013.png

So Beeswarm has improved a lot over the last few weeks, and I have
tried to summarize the changes here.

Traffic Pattern
---------------

In my last post, I had mentioned the Telnet and SSH Bees, which have much
more intelligence now. Along with them, the FTP and SMTP bees have been
updated too. The SMTP bee now uses a static mail archive (plans are to
replace this with something more dynamic) to send emails from. The ftp
client now checks what files are there on the Hive server before trying to
download it.

An important aspect of the traffic pattern was the ability to configure
when sessions should occur. In other words, the timings between the client
sessions should follow the real world patterns. Things were a bit complicated
here, because every protocol had to be handled differently. Now every bee
has a specific range of time (of the day) for which it is active. We also
have a slepp interval, and an activation probability. The activation
probability will decide whether the client session will be performed the
next time the bee is activated.

Loads of Bug Fixes/Improvements
-------------------------------

A lot of minor bugs were fixed (or features implemented).

1) Generation of self-signed cert/keys at startup
2) Fixing of the HTTPS capability, which resulted in "ssl_rx_record_too_long" errors.
3) Fixed a bug in packaging where one of the data files was not being copied.

All in all, getting things working is much more smoother now.

ISO Generation
--------------

This is one of the most exciting thing I've worked on. Ever. Beeswarm now
let's users download pre-configured, bootable ISO images of the new Hives/Feeders
which are created from the web-interface. And it's as simple as clicking "Download
ISO". There were quite a bit of challenges here, but I'll save them for my next
blog post ;)

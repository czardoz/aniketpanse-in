Beeswarm
========

:date: 2013-03-02
:tags: linux, honeypots, networking, security, beeswarm
:bg: /img/bgs/matrixlike.jpg



So I was looking for some good python projects to contribute to the other day,
and I came across `Beeswarm <https://github.com/honeynet/beeswarm/>`_, which
is another awesome project from HoneyNet. It basically consists of a honeypot,
the 'Hive', and automated 'Feeders' which simulate end users. i.e: They generate
traffic typically generated by active end users, such as uploading files, deleting
them on FTP, or running SSH sessions. The Hive part is completely implemented,
and Feeders are under active development.

At first I wondered what it'll be used for, and then I saw
`HoneyMap <http://map.honeynet.org/>`_, which not
only is insanely cool but is also used to gather high quality data for research.
The best part about this project is that it's relatively new, so there's a lot
I can help out with. I made my first contribution by fixing 3 small bugs. I
now want to work on fixing issue #8, upgrading pyFTPdlib to the latest version.
Let's see how that comes about! :)

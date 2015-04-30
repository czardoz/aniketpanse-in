Beeswarm - Week 3
=================

:date: 2013-07-21
:tags: beeswarm, gsoc, honeypots
:bg: /img/bgs/gsoc-2013.png

Ah a lot to talk about this time! BeeSwarm's coming along very well indeed.
There's much improved security, with respect to the communications between the BeeKeeper
(the Web-UI) and the Hive/Feeder. Hive is the actual Honeypot, while the Feeder
is a collection of clients which will attract traffic to the Honeypot.

The major part in the third week was to shift the web server to HTTPS. This
is important because the data about the captured attacks will likely be sent over the
same interface as the Honeypot, so there's always a chance that someone might be sniffing
the traffic. Sending JSON data over plain HTTP would definitely be a dead give-away.

Basically, securing the web API consisted of the following major steps:

* Changing the server, so that it uses SSL
* Updating the clients so they communicate over SSL too
* Since it's important to validate the data, it was also necessary to implement session support in the server/clients.

SSL Server
----------

Prior to working on this, Beekeeper ran on ``gevent.wsgi.WSGIServer``. This
particular implementation doesn't support SSL. Initially, the plan was to use `Gunicorn
<http://gunicorn.org/>`_.
However, I felt that such performance was not necessary here, since in all probability
the Web-UI will be used by only a few people at a time. That's why we decided to keep
things simple and use ``gevent.pywsgi.WSGIServer``. Both use the same API, which meant that
I had to make very few changes in the code itself. One major change was that I had to
create a self-signed cert for the BeeKeeper. This created some difficulties. Since version
1.0.x, `OpenSSL <http://www.openssl.org/>`_ generates private keys using different headers
than the previous version. Unfortunately, the pyOpenSSL wrapper doesn't yet support the
functionality to convert between different PrivateKey formats. This means that the certificate
has to be generated externally, before running the Beekeeper. It can be done this way:

.. code-block:: console

    $ openssl genrsa -des3 -out beekeeper.key 2048
    $ openssl req -new -key beekeeper.key -out beekeeper.csr
    $ openssl x509 -req -days 3650 -in beekeeper.csr -signkey beekeeper.key -out beekeeper.crt
    $ openssl rsa -in beekeeper.key -out beekeeper.key

The interesting step is the last step, which is the one where the passphrase is removed from
the key. This is the step that cannot be done in pyOpenSSL.

Anyway, the server finally did get going, and I next had to modify the clients so they worked
over SSL.

SSL Clients
-----------

The `python-requests <http://python-requests.org/>`_ library, which we're using in BeeSwarm
has great SSL support, so I didn't think there would be a lot of problems with it.
All I really had to do was store the certificate in the config file for the client
(which would be a Hive or Feeder) and pass ``verify=True`` as an argument while making the
request. I was wrong :) Actually, the problem didn't lie in the library, I just didn't
understand the documentation well enough.

After creating the self-signed cert and key, my server worked well with the browser
(I used Firefox/Chrome), however, it kept failing with EOF errors. At the same time,
the client failed with an "unknown CA" error. The server was failing because the client
refused to accept the certificate. Quite frankly, I had no idea how to get past this
problem. It was then that my mentor (`@johnnykv <https://github.com/johnnykv>`_)
pointed out that the cert was to be passed to the verify parameter, instead of just
passing a Bool. After that, it was pretty smooth sailing, and the client and it worked
like a charm :)

Logins
------

Another major functionality that was implemented in week 3 was a Login mechanism for
the Hive/Feeders. The idea was that each client must login to the Beekeeper before
transmitting any data. This means that the data should only be accepted from trusted
sources.

This part was not very hard to implement actually. it was done by adding a new User
into the database every time a new Hive/Feeder was created. The user ID would be the
same as the UUID used for the Hive/Feeder, while the password was another, randomly
generated UUID. This time, I had to use ``requests.Session`` which would handle the cookies
and other session business.

I had originally planned to merge the posts for weeks 3 & 4, but since this has
become so long, I guess I'll have to split it anyway. Expect another long post tomorrow!

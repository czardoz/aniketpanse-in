A Beeswarm Update
=================

:date: 2013-03-15
:tags: linux, security, honeypots, networking, beeswarm
:bg: /img/bgs/matrixlike.jpg

I had started actively contributing to Beeswarm and as I had mentioned
before I was working on adding pyFTPdlib 1.0 support. Turns out, since
we we only using a rather small part of it, I didn't have to do a whole
lot of changes. In the end I ended up adding support for a custom FTP
banner, which wasn't much work really. But I did get to learn a lot more
about the whole development process. I especially got to know a lot more
of git.

I then took up the task of adding support for HTTP 'Basic' Authorization,
as detailed in RFC 2617. Since I have already written a brute-forcer for
HTTP-Proxy Authorization, I thought this would not be very hard to implement,
considering Python's extensive HTTP support. I had anticipated a few snags
though, since I had to integrate this with HiveStreamServer, whereas the
documentation mostly consists of examples which use SocketServer.TCPServer.
Since the request handling is done by BaseHTTPServer, I tried to integrate
it with Beeswarm. This led to a situation where I had two different servers
running (for the HTTP capability). Here's the code for the capability itself:

.. code-block:: python

    class http(HandlerBase):

        """The HTTP Capability"""

        def __init__(self, sessions, options):
            super(http, self).__init__(sessions, options)
            self._options = options

        def handle_session(self, gsocket, address):
            session = self.create_session(address, gsocket)
            handler = BeeHTTPHandler(gsocket, address, None, httpsession = session,
                                        options = self._options)


Here the gsocket is the one created by the Hive, for my capability. The
problem I ran into was, that there was no way of passing my own socket to the
BaseHTTPServer class. I was rather stuck over here, but then the Lead Developer
(who's been mentoring me), pointed out that I can use the \*Handler classes
without a Server, by instantiating them directly, with the first argument as our
socket. Once this was figured out, it was pretty straightforward writing the
BeeHTTPHandler class.

.. code-block:: python

    class BeeHTTPHandler(BaseHTTPRequestHandler):

        def __init__(self, request, client_address, server, httpsession, options):
            self._options = options
            if self._options.has_key('banner'):
                self._banner = self._options['banner']
            else:
                self._banner = "Microsoft-IIS/5.0"
            self._session = httpsession
            BaseHTTPRequestHandler.__init__(self, request, client_address, server)

        def do_HEAD(self):
            ...

        def do_AUTHHEAD(self):
            ...

        def do_GET(self):
            ...

The do\_\* methods are pretty standard, so I won't bother elaborating on those.
The interesting part is where I had to call the parent constructor after
initializing a few attributes, since it calls methods which use these attributes.
I added support for a user-definable 'banner' which will allow the Hive to
'impersonate' other servers, such as Microsoft-IIS or Apache. I also wrote a
unittest, which uses the httplib to connect to the server and verifies whether
the server is sending 401-Unauthorized status code.

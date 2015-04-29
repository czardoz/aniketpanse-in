Beeswarm - Week 4
=================

:date: 2013-07-29
:tags: beeswarm, gsoc, honeypots

This week was all about making the Bees (the automated sessions on the Honeypot)
look more legit. So, at the first glance, it should be able to be convincing
enough for a potential attacker to think that this is a real user using the system.

The best way to make the Bees more 'intelligent' was to implement a Sense ->
Decide -> Act loop (SDA). This involved making a lot of changes to the bees, and
the way data is sent over the raw sockets.

1. Telnet & SSH
---------------

Since the basic functionality offered by these two protocols is the same, I decided
to write a common backend for both. Designing this meant that I needed a common
high-level API for both the Bees, which would allow me to handle the data flow in
an easy way, using a common base class.

Another interesting change that I made was emulating user keystrokes on the Telnet Bee.
Using the timing data from `this paper <http://www.cs.berkeley.edu/~dawnsong/papers/
ssh-timing.pdf>`_, along with Python's great ``random`` library, I
was able to generate appropriate intervals and emulate the general typing characteristics
of a person. This was done in by overriding the write() method of the ``telnetlib.Telnet``
class.

.. code-block:: python

    def write_human(self, buffer_):
        """ Emulates human typing speed """

        if self.IAC in buffer_:
            buffer_ = buffer_.replace(self.IAC, self.IAC+self.IAC)
        self.msg("send %r", buffer_)
        for char in buffer_:
            delta = random.gauss(80, 20)
            self.sock.sendall(char)
            time.sleep(delta/1000.0) # Convert milliseconds to seconds

In order to make the SDA loop realistic, I created a simple data structure, a sort
of mapping, which would help the Bee choose the next command to execute. Something
like this:

.. code-block:: python

    COMMAND_MAP = {
        'pwd': ['ls', 'uname', 'uptime'],
        'cd': ['ls'],
        'uname': ['uptime', 'ls'],
        'ls': ['cd', 'cat', 'pwd'],
        'cat': ['ls', 'echo', 'sudo', 'pwd'],
        'uptime': ['ls', 'echo', 'sudo', 'uname', 'pwd'],
        'echo': ['ls', 'sudo', 'uname', 'pwd'],
        'sudo': ['logout']
    }

Here, the RHS for every command contains commands that can be run after it. So, in general,
a ``cd`` should be followed by an ``ls``, and so on.

Along with these changes, I also improved and refactored the code a lot. This time,
we also have test cases for each command, and I'm beginning to see the benefits of
Test-Driven-Development. Next week will be dedicated to the rest of the Bees. Now
that I have a clear idea of what needs to be done, I hope I'll be able to finish the
work on all of them.

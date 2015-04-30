More Kate
=========

:date: 2013-03-02
:tags: linux, kde, Qt
:bg: /img/bgs/kate.png


As I said before, I'm currently playing around with Kate. I must
say I've learnt a lot in this week. I was able to compile my own
little plugin, wrote it from scratch (referring to the helloworld
code of course!). It doesn't do anything interesting right now,
but I plan to make it do something cool.

The idea is to listen on a TCP socket and insert whatever data I
receive on that socket. I have no idea right now where this code
should go. I can make a standalone server easily, but integrating
that into the callback based model of a GUI system might be pretty
tough. I am also reading up on C++, and plan to start with Telepathy
next week. I should probably also mention what exactly I am doing
when I debug Kate: (from within the build directory)

.. code-block:: console

    $ make
    $ make install
    $ ../run.sh kate 2>&1 | grep <my-debug-prefix>

The '2>&1' part means that the stderr is redirected to stdout.
That's how I can grep :) I assumed that I won't have to run CMake
everytime. Also, it's becoming really tedious, using vim and shell
all the time. I will have another go at setting up the whole thing
with QtCreator, I might even get to learn something in the process!

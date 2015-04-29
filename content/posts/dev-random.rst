/dev/random Device
==================

:date: 2013-02-16
:tags: linux, random-number-generator



I read about the 'devrandom' device in linux today. It's a random number
generator which uses environmental noise to generate the random numbers.
That's pretty cool, because that makes it as close to a true random number
generator as possible. It also uses our mouse movements and stuff to feed
the 'entropy pool' as it's called.

The only drawback of this device is that it 'blocks' when the entropy pool
is empty. That means, if any program is using /dev/random and the entropy
pool becomes empty, the program must wait for it to be refilled again.
Now, that's pretty unacceptable in some cases. Which is why we have the
/dev/urandom device. It is basically an unlocked device (means it won't
block). It reuses the existing entropy pool to generate more pseudo-random
bits. Yep, that means it's not truly random.

If you want to generate cryptographic keys, maybe /dev/urandom is not the
way to go. If you really want true unblocked random numbers, you should read
this article. It mentions a way of using white noise from your soundcard
to ensure that the entropy pool doesn't get depleted. Use the audio entropy
daemon or video entropy daemon to collect entropy from sound or video data.
Just as an interesting aside, FreeBSD doesn't implement the devrandom device
in this way. It uses a different implementation, which allows /dev/random
to be non-blocking by itself.

I moved my blog!
================

:date: 2013-05-19
:tags: blog, web, static-blog

Been a while since I've been active. I've been procrastinating writing
new stuff because it was such a pain to write all my posts in pure HTML.
I needed something better, so I could focus on the content, rather than
worrying about the HTML tags. I needed a nice little framework, which
would let me write, and handle CSS, colors and links for me.

Now for most people, Wordpress would suffice. Actually, it won't just
suffice, people will start loving it. It has just about everything a blogger
would want. Tags, themes, comments, plugin extensions, you name it and
it's there. But with that comes a load of bloat. Plus I didn't want
something that's PHP based. (I've got nothing against PHP, but it just
`doesn't seem very elegant <http://phpsadness.com/>`_).

I went through a few other blogging platforms, and ended up liking
none of them. Basically, I needed these features in my framework:

* Support for posts in Markdown or reStructuredText
* Easy deployment (should take less that 3 minutes)
* Extensible
* Good theme support

Static blog generators did fit the bill well. The added advantage of
static generators is that I don't need a hi-fi server to host my website.
There's an insane amount of choice when it comes to static website
generators. We have them in Ruby, Python, Java and there's even one `in
Haskell <http://jaspervdj.be/hakyll/>`_. A quick search on GitHub shows
159 repositories. You might also want to check out the `list of static
website generators <https://github.com/pinceladasdaweb/Static-Site-Generators>`_.

To summarize, there are a lot of static website generators. And it's
really hard to choose one. After trying to work with Jekyll, Hyde,
Tinkerer, Pelican, Obraz, Poole, Acrylamid, I finally came across `Felix
Felicis <http://lab.lepture.com/liquidluck/>`_. What attracted me about it (apart from the name!) is that it
is extremely easy to search for, install and modify themes. I am
currently using a sort of modified version of the default theme.

I'll do a short write up on how to set everything up in my next post.
However, the `documentation <http://liquidluck.readthedocs.org/en/latest/>`_ is really worth checking out.
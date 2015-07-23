Deploying to GitHub from Travis
===============================

:date: 2013-07-23
:tags: travis-ci, autodeploy, static-website, github-pages
:bg: /img/bgs/automatic.jpg

Small automations sometimes really help your workflow. That's the case with my blog too.
I use a static blog generator to generate my blog, and GitHub pages to host the generated
HTML. This is a problem, because I have to maintain two distinct repositories, one to
host my blog content, aniketpanse-in_ and one to host the blog itself, `czardoz.github.io`_.
I have to make sure that both the repositories are always kept in sync.

I used `Travis-CI <http://travis-ci.org/>`_ to automate this job for me. Every time I make
changes to the ``master`` branch of `aniketpanse-in`_, Travis builds the changes, and
pushes them to `czardoz.github.io`_ for me.

.. _aniketpanse-in: https://github.com/czardoz/aniketpanse-in
.. _czardoz.github.io: https://github.com/czardoz/czardoz.github.io

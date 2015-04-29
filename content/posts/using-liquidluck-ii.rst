Using Liquidluck (Felix Felicis) - Part II
==========================================

:date: 2013-05-23
:tags: blog, web, static-blog, liquidluck

This is a continuation of the `Part-I </2013/using-liquidluck.html>`_,
of the Liquidluck tutorial. Upto this point, we have discussed the following:

* Installation
* Setting up the Website
* Writing Posts/Pages

We shall now continue with themes, and also touch upon publishing the websites
on GitHub.

Themes
------

Now that you can write posts and pages, I guess you will want to tweak the overall
look or 'feel' of your website. 'Luckily', Liquidluck has awesome support for the
installation and search of new themes. Here's how you can search for new themes:

.. code-block:: console

    (felix_env)$ liquidluck search

It'll give you a nicely formatted output with the theme name and stuff. If you like
the name of any of the themes, you can install it like this:

.. code-block:: console

    (felix_env)$ liquidluck install <theme-name>

Pretty easy, eh? It sure would be great to preview the themes though, if possible.
So what you can do is, install all the themes in one go and try them out, one by one.
I had some problems with installing a few of the themes from GitHub, so I wrote the
following script to do it for me. Basically, it clones the theme repos into our `_themes`
directory, so that they are immediately available to Liquidluck.

.. code-block:: bash

    git clone git://github.com/lepture/liquidluck-theme-chiang.git ./_themes/chiang
    git clone git://github.com/lepture/liquidluck-theme-moment.git ./_themes/moment
    git clone git://github.com/bingdian/liquidluck-theme-responsive.git ./_themes/responsive
    git clone git://github.com/bcho/liquidluck-theme-cb3f20.git ./_themes/cb3f20
    git clone git://github.com/lianqin7/liquidluck-theme-icbu.git ./_themes/icbu
    git clone git://github.com/microjo/liquidluck-theme-lovelymod.git ./_themes/lovelymod
    git clone git://github.com/lepture/liquidluck-theme-bootstrap.git ./_themes/bootstrap
    git clone git://github.com/lepture/liquidluck-theme-octopress.git ./_themes/octopress
    git clone git://github.com/leandromouta/liquidluck-theme-thegrey.git ./_themes/thegrey
    git clone git://github.com/popomore/liquidluck-theme-reveal.git ./_themes/reveal

Note that you should run this script from the root of your website. That is, the parent
of the ``contents`` directory. You can customize/modify the themes easily, since they use
the Jinja2 templating language. The `templating part <http://blog.miguelgrinberg.com/post/
the-flask-mega-tutorial-part-ii-templates>`_ of the Flask mega tutorial gives an
awesome introduction to Jinja2 templates, so you might want to go through it.

Publishing on GitHub
--------------------

Now that you have (hopefully) decided on the theme, and are able to write new posts and
create pages, you might want to publish your website. One of the easiest ways to do this,
is on GitHub itself. In order to host your website on GitHub, you first need an account
on GitHub. If you're already familiar with Git in general, then all you need to do is,
create a new repo with the name ``<username>.github.io``, and push the pages (in the deploy
folder to this repo. They will automatically be served by GitHub, on the URL
``<username>.github.io``. (Replace 'username' with your actual username!)

If you're not familiar with it, create an account on GitHub. Once you've registered and
your email address is verified, you can create new repos. In order to create a new repo,
click on the New Repo button (right next to the settings button). In the 'repository name'
field, enter ``<username>.github.io`` (Replace the username with your own!). Note this
name, we're going to use it later.

Now, on your local machine, create a new git repository in the ``deploy`` directory.

.. code-block:: console

    (felix_env)$ cd deploy
    (felix_env)$ git init .
    (felix_env)$ git add .
    (felix_env)$ git commit -m "initial commit"
    (felix_env)$ git remote add origin https://github.com/czardoz/<repo-name>.git
    (felix_env)$ git push -u origin master

Note that you must replace <repo-name> with the repository name that you have given.
If you've done it all properly, you can now see your website hosted on https://<username>.github.io!
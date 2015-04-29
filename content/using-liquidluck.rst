Using Liquidluck (Felix Felicis) - Part I
=========================================

:date: 2013-05-23
:tags: blog, web, static-blog, liquidluck

This post is a follow up to my `previous post </2013/moving-to-liquidluck.html>`_.
Although the liquidluck documentation is all you really need, I'll try
to explain how I made my personal website, and published it on GitHub.

Installing 'Felix Felicis'
--------------------------

Liquidluck is available as a PyPi package, so you can install it in the
standard way. It's always better to install in a Virtual Environment, so
that it keeps out of the way and doesn't interfere with your base Python
installation. In order to do this, you need to install virtualenv itself.

.. code-block:: console

    # pip install virtualenv

If you don't have pip, you can use easy_install, like this:

.. code-block:: console

    # easy_install -U pip

There are a `lot of reasons <http://stackoverflow.com/questions/3220404/why-
use-pip-over-easy-install>`_ why pip is preferred over easy_install.
Anyway, once you have pip, you can use it to install virtualenv. You can then do:

.. code-block:: console

    $ virtualenv --no-site-packages felix_env

This will create a new directory (a special one which is called a virtual
environment) in your current working directory. You can now install new Python
packages by 'activating' this new environment and using pip. Something like:

.. code-block:: console

    $ source ./felix_env/bin/activate
    (felix_env)$ pip install <package-name>

Notice how the prompt changes, to indicate which virtualenv you are currently
using. This is dead useful when we need to work with multiple environments.
You can now install liquidluck as outlined above, just use liquidluck as the
package name. If you're like me, and want to use the latest 'bleeding edge'
version, instead of using the standard package name, you can use the git version
as follows:

.. code-block:: console

    (felix_env)$ pip install git+https://github.com/lepture/liquidluck.git

Setting up the Website
----------------------

We are now ready to actually start building the website. Let's call the new
website 'newblog'. You should start by creating a new directory for our blog,
and tell liquidluck to set everything up for us.


.. code-block:: console

    (felix_env)$ mkdir newblog
    (felix_env)$ cd newblog
    (felix_env)$ liquidluck init

You will then be asked for your config file format, for which I chose Python (and that's the format I'll be using in this tutorial). I kept the posts and output folders at their default values.

Now that the directory is initialized, we must make a few changes in the config
file. I recommend that you set at least the site name, the site URL, the Author
name, and uncomment reStructuredText reader. If you don't want to end up with
broken tag links, you might also uncomment all the Writers. Also set any other
variables you wish to, the default file is pretty well commented. Ultimately, you
should end up with a config file like this (comments removed for brevity):

.. code-block:: python

    site = {
        "name": "Your Name",
        "url": "http://some-website.com",
    }

    config = {
        "source": "content",
        # ... A few more here ... #
        "timezone": "+00:00",
    }


    author = {
        "default": "Your Nickname",
        "vars": {}
    }

    reader = {
        "active": [
            "liquidluck.readers.markdown.MarkdownReader",
            "liquidluck.readers.restructuredtext.RestructuredTextReader",
        ],
        "vars": {}
    }

    writer = {
        "active": [
            "liquidluck.writers.core.PostWriter",
            # ... a few more here ... #
            "liquidluck.writers.core.CategoryFeedWriter",
            "liquidluck.writers.core.TagWriter",
            "liquidluck.writers.core.TagCloudWriter",
        ],
        "vars": {
        }
    }

    theme = {
        "name": "default",
        "vars": {
        }
    }

    template = {
        "vars": {},
        "filters": {},
    }

Now that the website is set up, we are ready to create our first blog post.

Writing a Post
--------------

Felix Felicis supports posts in Markdown, or in reStructuredText. Choosing any
of them is up to you. Personally, I find rST to be more flexible, offering a
richer set of features and so, I'll be using it in this tutorial. (As an interesting
aside, this blog post is also written in rST).

Let's call our first post 'Hello Felix':

.. code-block:: console

    (felix_env)$ vim content/hello-felix.rst

This will open up the vim editor (You can use any text editor you like).
You can then type the content of the post, and save the file. If you're too lazy
to type, and just want to get the thing working for now, use the following
block of code:

.. code-block:: restructuredtext

    Hello Felix!
    ============

    :date: 2013-02-15
    :tags: blog, web
    :category: misc

    Hello Felix! This is my first post! :-)

Once you save the file, you can see the result by 'building' the website and then
viewing it in your browser. For that you must also run the liquidluck server:

.. code-block:: console

    (felix_env)$ liquidluck build
    (felix_env)$ liquidluck server

You can now view the new website by visiting http://127.0.0.1:8000. The awesome
part about this server is that it automatically refreshes your browser tab as
and when you make changes to your website! I've had too much experience of
refreshing a webpage every time I make any change in my code, so I fully
appreciate the utility of this awesome little tool.

Adding a Page
-------------

Most of the times, you want to add a page to your site, say 'About', or 'Contact'.
Liquidluck supports the generation of static web pages too. The only difference
between a post and a page is that a page doesn't have a date associated with it.

Let's add an 'About' page,

.. code-block:: console

    (felix_env)$ vim content/about.rst

That has the following content:

.. code-block:: restructuredtext

    About:
    ========

    Some stuff I want to say about myself.

Build the site again, and view the new page in your browser, as outlined before.
You'll have to visit http://127.0.0.1:8000/about.html in order to see the new page.

That's all for the first part. My next post will deal with publishing the website
on GitHub. See you then!

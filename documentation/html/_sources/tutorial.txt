.. highlight:: bash

.. _tutorial:

Create Your First Spontaneous Site
=======================================

This will walk you through creating your first Spontaneous site. Because blogs
are boring we're going to look at the creation of a simple recipe site that
will let you publish your favorite recipies.

Before you start you should have worked through the :ref:`installation`.

To make sure that Spontaneous is installed and works try running the
included ``spot`` program::

    $ spot version

If this doesn't work then you don't have Spontaneous installed or something is
stopping it from working. In that case please go have a look at the
:ref:`installation-troubleshooting` section.

.. todo::
    ``spot version`` actually prints the current version of Thor... not much
    good at all!

Generate a Website Skeleton
---------------------------

Use the ``spot`` executable to generate a new site, in this case ``example.com``::

    $ spot generate example.com


Change into our newly generated Spontaneous site directory::

    $ cd example_com

Use `bundler <http://gembundler.com/>`_ to install the dependencies (this make take a minute or so)::

    $ bundle install

Now use the ``spot`` command to initalize the site. This will create the
necessary databases and database tables::

    $ spot init

.. note::
    This requires administrator access to your database in order to do its work. It
    should hopefully work out of the box but you may need to pass in extra
    authenticaion parameters in order to correctly access the database.

.. todo:: Look at spot init and include login parameters

Create Your Schema
------------------

Spontaneous uses basic Ruby code to define its content models. You don't need
very much knowledge of Ruby in order to define the schema for a website, but
a basic familiarity with the language will make things much easier.

Run the Development Server
--------------------------

.. code-block:: bash

    $ spot server

Use the CMS User Interface to Build Your Site
---------------------------------------------

Create HTML Templates
---------------------

Preview Your Site
-----------------

Publish Your Changes
--------------------

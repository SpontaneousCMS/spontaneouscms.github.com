.. _installation:

Installation Guide
==================

This guide will walk you through installing Spontaneous and the software that
it requires.

Spontaneous is a Ruby program so first we'll need to :ref:`install-ruby`.

.. _install-ruby:

Installing Ruby
---------------

Spontaneous currently requires MRI Ruby >= 1.9.3

Install RVM::

    $ rvm install 2.0.0


Installing Spontaneous
----------------------

.. code-block:: bash

    $ gem install spontaneous --prerelease --no-rdoc --no-ri

This installs the Spontaneous library files (and their dependencies) as well as
the executable ``spot``.

Installing a Database
---------------------

Spontaneous is tested & works with MySQL 5+ and PostgreSQL 9+ and is currently
completely agnostic about the choice.

[This may change in a later version with the way that PostgreSQL is
developing (with native JSON and hash support).]

On MacOS X we recommend you use `homebrew <http://mxcl.github.com/homebrew/>`_
to install either of these.

.. code-block:: bash

    $ brew install postgresql
    # or
    $ brew install mysql


.. _installation-troubleshooting:

Installing ImageMagick
----------------------

Image fields can specify any number of generated 'sizes' with specific postprocessing
steps such as resizing, cropping, colour conversions overlays etc. This image
manipulation is done using ImageMagick so you'll need to install it before continuing.

On Mac OS X this is easiest using Homebrew:

.. code-block:: bash

    $ brew install imagemagick


Troubleshooting
---------------

Has something gone wrong?

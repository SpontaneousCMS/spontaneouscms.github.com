.. highlight:: bash

.. _tutorial:

Spontaneous CMS Tutorial
=======================================

This will walk you through creating your first Spontaneous project. In this
tutorial we're going to look at the creation of a simple site that will let you
publish your favorite recipies.

Before you start you should have worked through the :ref:`installation`.

To make sure that Spontaneous is installed and works try running the
included ``spot`` program::

    $ spot version

If this doesn't work then you don't have Spontaneous installed or something is
stopping it from working. In that case please go have a look at the
:ref:`installation-troubleshooting` section.

Generate a Website Skeleton
---------------------------

Use the ``spot`` executable to generate a new site, in this case ``example.com``::

    $ spot generate example.com


Change into our newly generated Spontaneous site directory::

    $ cd example_com

Use `bundler <http://gembundler.com/>`_ to install the dependencies (this make
take a minute or so)::

    $ bundle install

Now use the ``spot`` command to initalize the site. This will create the
necessary databases and database tables::

    $ spot init

.. note::
  This requires administrator access to your database in order to do its work. It
  should hopefully work out of the box but you may need to pass in extra
  authenticaion parameters in order to correctly access the database.

.. todo:: Look at spot init and include login parameters

.. highlight:: ruby

Create Your Schema
------------------

Spontaneous uses basic Ruby code to define its content models. You don't need
very much knowledge of Ruby in order to define the schema for a website, but
a basic familiarity with the language will make things much easier.

Let's first think about what we're trying to achieve: a homepage that shows a
list of recipes and a set of recipe pages that have:

- A recipe title
- A short description of the recipe
- A photo of the finished dish and
- A list of ingredients,
- A set of instructions (each instruction with an optional photo)

Let's start with the recipe pages. To define the content structure of a recipe
page we need to add a ``Recipe`` content type and then define the various fields
and boxes that are needed in order to build our final recipe page.

First we create a file in the ``schema`` directory called ``recipe.rb``::

    # schema/recipe.rb

    class Recipe < Page
    end

The line beginning with ``class`` is where we define our content type. In this
case we are calling it ``Recipe`` -- all content types are named with a capital
letter. We want our recipes to be directly accessible in the browser so we know
they are pages. The part "``< Page``" can be read as "extends Page" or "inherits
from Page".

Defining Editable Fields
^^^^^^^^^^^^^^^^^^^^^^^^

So now we have a page type called "Recipe". Now we need to define the editable
fields that each recipe needs (title, description and photo)::

    # schema/recipe.rb

    class Recipe < Page
      field :description, :text
      field :photo,       :image
    end

Fields are defined by adding a call to the ``field`` directive within the body
of the content type definition. The syntax is::

    field :<field_name>, :<field_type>, [options...]

``field_name`` is what you'll use to refer to the value of the field within your
templates (see later) and also the name that will show up in the editing
interface.

If you wanted to use a different name for the field in the user interface, you
would pass a value for ``title`` in the field options::

    field :description, :text, title: "A short description"

This would change the name of the field in the user interface to "A short
description" but the internal name of the field would still be "description".

The second parameter to the field definition is the field type. The basic field
types are ``:string``, ``:text`` and ``:image`` (though there are more see
:ref:`schema-field-types`).

**String** fields are basic bits of text without any formatting. They are
useful for titles and any other bit of text that you want to appear as unstyled.
``string`` is the default type, so if you want to create a string field you can
skip the type parameter::

    # defaults to a ``string`` field
    field :name

**Text** fields allow for the entry of rich text. These are useful for body text
and allow the content editors to style text as bold or italic, add lists of
items, headers and hyperlinks. The default (and currently only) input mechanism
is a `Markdown <http://daringfireball.net/projects/markdown/>`_ editor.

.. note::
  **Why not WSIWYG?** If you've ever stuggled with a browser based `WYSIWYG
  <http://en.wikipedia.org/wiki/WYSIWYG>`_ you'll know that things don't always
  go to plan. One of the design philosophies of Spontaneous is to allow content
  editors to produce richly styled web-pages without having to struggle with
  layout themselves and without being in danger of breaking the layout of the
  pages. WYSIWYG editors actually hinder this. Having said that, when browsers'
  WYSIWYG implementations improve enough to be reliable and produce safe and
  uncluttered HTML then we'll think about using them.

**Image** fields allow you to upload images using a drag-and-drop interface.
Spontaneous has very powerful image manipulation functions built into it which
allow you to define & generate multiple different versions of each uploaded
image. For more information see :ref:`schema-field-types-image`

One useful shortcut when defining fields is that if no field type is given then
it will first try to find a field type based on the name of the field. So, for
example::

    field :image
    # is exactly the same as
    field :image, :image

Each field type also has some useful aliases, for example image fields can be
referred to as ``photo`` fields::

    field :photo
    # is the same as
    field :photo, :photo
    # which is the same as
    field :photo, :image

So our Recipe page definition could also have been written like this::

    # schema/recipe.rb

    class Recipe < Page
      field :description, :text
      field :photo
    end

Where's the title?
******************

You may have noticed that although we said that each Recipe page should also
have a recipe title, the above Recipe type definition has no title field
defined. How come?

The secret is in the ``Page`` content type that ``Recipe`` inherits from. This
``Page`` type is defined in the ``schema/page.rb`` file that Spontaneous will
have generated for you. It will look something like this::

    class Page < Spontaneous::Page
      field :title, :string, :default => "New Page"
    end

Because our ``Recipe`` type is inheriting from this ``Page`` class we also
inherit its fields, in this case the title field.

.. note::
  This is a very powerful feature of Spontaneous's type system. It allows you to
  share field values (and more) between different content types. This way you can
  have many different content types that are mostly the same but differ in a few
  key areas. To read more about this inheritance model see
  :ref:`schema-inheritance`.

Note that the ``title`` field has a ``default`` option specified in its
definition. This option allows you to give a default value for the field. In
this case we're specifying that each new page should be called "New Page".

Hmm, "New Page" isn't very good for new recipes, so what we can do is
re-define the title field inside our Recipe class and change this default value
to something more appropriate::

    # schema/recipe.rb

    class Recipe < Page
      # we can drop the type for the `title` field
      # because the default is :string
      field :title,       default: "New Recipe"
      field :description, :text
      field :photo
    end


For more details on defining fields see :ref:`schema-fields`.

Now we have the fields defined for our Recipe type, but what about the
ingredients and instructions? For that we need to learn how to use the next most
important element of the Spontaneous schema: Boxes.

Creating and Filling Content Boxes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Spontaneous boxes allow you to fill a page with any amount of additional
content. They are 'holes' in our pages that we configure to accept the addition
of certain other content types. Within the CMS user interface these boxes allow
you to add, remove and re-order their content with an intuitive drag-and-drop
interface.

The content of boxes can be either pages or "pieces". We have described pages as
content that has its own URL and is directly accessible within the browser.
"Pieces" however only exist within pages and don't have URLs. They do share the
same ability to have fields (and boxes) and are defined in almost exactly the
same way.

So, let's create an "Ingredients" box that will hold our list of ingredients. We
do this within our ``schema/recipe.rb`` file as before::

    # schema/recipe.rb

    class Recipe < Page
      # we can drop the type for the `title` field
      # because the default is :string
      field :title,       default: "New Recipe"
      field :description, :text
      field :photo

      box :ingredients do
        allow :Ingredient
      end
    end

This adds the ingredients box and specifies that we want to allow the user to
add items of type "Ingredient". Now we need to define the "Ingredient" type.

.. note::
    We will do this in the same file as the Recipe type for convenience but it's
    usually a good idea to stick to a one-type-per-file rule.

.. code-block:: ruby

    # schema/recipe.rb

    class Ingredient < Piece
      field :name
      field :quantity
    end

Our "Ingredient" type is pretty simple, just a name and a quantity.

Now we need to create the recipe itself by allowing the user to enter a set of
instructions:

.. code-block:: ruby

    # schema/recipe.rb

    class Recipe < Page
      # we can drop the type for the `title` field
      # because the default is :string
      field :title,       default: "New Recipe"
      field :description, :text
      field :photo

      box :ingredients do
        allow :Ingredient
      end

      box :instructions do
        allow :Step
      end
    end

    class Ingredient < Piece
      field :name
      field :quantity
    end

    # Step entries are added to the "instructions" box within our Recipe
    class Step < Piece
      field :instructions, :text
      field :photo, comment: "Optional"
    end

Our Recipe type now has an "instructions" box that allows the user to add any
number of "Step" pieces. Each Step has a set of instructions and a photo. The
photo field has a ``comment`` set -- this will appear in the user interface in
order to offer some guidance to editors. In this case we're telling them that
they don't have to have a photo for each step.

That's enough to power our recipe site.

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



Introduction
===========================================

Spontaneous takes the traditional idea of an online content management system
and brings it leaping into the future.

In this future your content is not constrained into one-dimensional streams of
identical content items but is instead a richly structured hierarchy of
information. Instead of constraining you within a pre-determined model of the
way your site is constructed, Spontaneous allows you to create, maintain and
extend your own customised set of relationships and hierarchies.

That is not to say that it's complex or difficult to use. Completely the
opposite is true in fact. Because your information is structured in the way that
you understand, things live where you expect them to live and there is usually
a very direct relationship between the content you enter into the CMS interface
and the content of the final rendered HTML page.

.. note::
  **"Content"**. We all hate the term. It demeans all the effort you put into
  crafting a beautiful website that tells what you hope is a fascinating story.
  But for the purpose of this documentation it really is the most
  straight-forward and direct way of describing the mixed bag of text, images,
  videos, links etc. that go to make up a website. So, sorry now let's move on...

How Spontaneous Works
---------------------

Building a Spontaneous site is best started by getting a really good feel for
the basic building blocks of your site.

Spontaneous breaks content down into "types". Each content type has at least a
name and a set of zero or more "fields".

These types are defined within a site "schema" which is a set of Ruby source
files.

If you are building a blog site for example then your basic unit is a blog
"post". This post will need to have at least a title, a short synopsis, perhaps
a representative image and then an area where you can add in the body of the
post. This post body will be a mixed bag of hyper-text, images and videos.

To represent this "post" in Spontaneous's type system you would need the
following code::

    # schema/blog.rb

    class Post < Page
      field :title
      field :synopsis
      field :image

      box :body do
        allow :Image
        allow :Text
        allow :Video
      end
    end

    class Image < Piece
      field :image
      field :alt, :string
    end

    class Text < Piece
      field :text
    end

    class Video < Piece
      field :video, :webvideo
    end

If your site's homepage is just a list of your blog posts then you would
represent it like this::

    # schema/homepage.rb

    class Homepage < Page
      box :posts do
        allow :Post
      end
    end

This is the complete Ruby schema of a working blog site.

Obviously real-world examples will most likely be more complicated than this,
involving many more types and fields, but the basic building blocks are exactly
as above.

In order to turn this schema into a web-page you need two more things: some
blog posts and a set of HTML templates that will convert the posts into a
webpage. If you want to see how that works then dive into the :ref:`tutorial`
which will guide you through installation and show you how to convert a basic
schema into a real webpage.



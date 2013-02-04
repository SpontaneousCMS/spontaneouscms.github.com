---
layout: post
title: "A Spontaneous History"
tags: []
published: true
---
## A Spontaneous History


**Or**

### One man's long search for the perfect CMS system.

Spontaneous has a long and less-than-glorious history. It's origins lie in the murky past when PHP ruled everything and Netscape 4 was king.

This is an attempt to describe how the ideas that started in that murky past crystallised into the Ruby powered CMS available today.

## A Vision

Right from start my vision was that a bit of content entered into a CMS system should know how to represent itself as HTML. I wanted object-oriented content and I was willing to jump through any hoop in order to get it.

At this point web-technology was very primitive. PHP 4 had just come along which meant that, for the first time, you got session tracking built into your server-side application.

Every website I built, I built pretty much from scratch. This was because every time I built a site I learnt more and the code I'd written before, the assumptions I'd made, seemed hopelessly naive.

So I re-wrote from scratch hoping that this time I would avoid those mistakes.

This meant a lot of mistakes, a lot of very badly written codebases, but it also meant I wrote a lot of web applications from scratch and a lot of iteration.

## Attempt 1

After all that PHP this first version ended up being based on a totally different technology stack.

The mandated platform was Windows IIS & SQL Server. From bitter experience there was no way I was going to write anything in ASP every again. So instead I chose Perl.

Perl, XML and XSLT.

That was my first mistake.

My abitious vision for this CMS was that all editing of the site would be done inline. The users would browse the site as normal and the backend would insert small buttons around editable parts of the page that would allow the user to change the content.

I'm calling this version 1, ignoring other attempts, because it was the first one that divide the content into types with each type having a set of defined fields, each of which had a specific type. The editing interface popped open forms generated from the content definition.

The system worked and powered the site in question for years, adapting & growing in ways that I would never have predicted but able to cope with each new demand.

I learnt some important things from this first attempt:

* I hate Perl
* XML is not a good database format
* You can do anything with XSLT but you might just lose your mind doing it, and more seriously
* **Editing content inline restricts your design too much.**

	The first thing I had to do, when I recieved the final designs for the site, was to re-write the editing interface because the designed page elements were just too small to hold an interface. Instead of of 'inline' editing I was forced to adopt a weird 'overlay' design that was awkward
* **The content 'type' and field list model works really well.** Being able to generate standard interfaces from a set of defined field types is very flexible and the separation of content into discrete pieces enabled me to work within the aforementioned design constraints
* **Code generation is extremely powerful**. What made the final system so powerful & flexible was that the XML+XSLT transformation system treated all its content as just text. It didn't matter whether the result of the transformation was plain HTML, or an ASP page or Javascript, it just spat it out and named it as it was told.

	The dynamic elements of this site were driven using ASP. To integrate CMS driven content with ASP driven user interaction all I had to do was make the CMS spit out HTML with ASP tags name them correctly with the ".asp" extension and put them into the right place. The server would do the work of making them dynamic. It seems obivous now but as I developed the approach it was a real revelation.

## Attempt 2

Version 2 was in some ways a backwards step from version 1. It was a more classic technology stack, PHP & MySQL, but something about this actually made reproducing some elements of the version 1 functionality more difficult.

The main step forward compared to version 1 was that content editors were able to add new pages. In version 1 new pages had to be manually added by copying & pasting chunks of XML. Given the requirements this was acceptable for the first site, but it was obviously flawed.

Version 2 allowed for the dynamic creation of pages but the code for doing this was very poor and fragile. This was probably something I could have refactored away with enough time but I still get the sweats thinking about the main update function in that code.

So when it came time for version 3, I had no qualms about starting again.

## Attempt 3

This is the version that finally started to get things right. I started to write this at the point where web frameworks had started to change internet programming for ever (and for the good) so for the first time I had some real choices to make.

I had just come from a lot of development in the Python web framework [TurboGears](http://turbogears.org/). I'm sure it's changed beyond all recognition  since then (2006) but the experience had left me less than happy. I also had a look at Django which seemed to tick the boxes (I knew Python and it sold itself on its speed) but one look at the API documentation left me cold. I found the method names difficult to remember and the overall feel seemed wrong somehow. Plus, I wasn't sure I liked Python after all that time spent using it (`"".join(["W", "T" , "F"]`).

So I had a look at Rails, which had just gone to version 1. I didn't know Ruby at all, but I immediately loved it and launched into version 3 of the CMS.

Active Record solved all the problems I'd been having in the PHP version. Validations & default values, easy to use associations, `after_` and `before_` hooks, the mapping of a db table to a class and a table row to a class instance… It made all the difficult work of version 2 seem easy and the system came together easily (considering my lack of Ruby or Rails experience).

Finally I had blocks of content that knew how to render themselves into a page and a user interface that looks crude now but at the time was as cutting edge as it got -- drag & drop reordering, user interface updates via AJAX, subtle fades & transitions -- exciting stuff at the time.

In this version, content types were defined in the database. Types were defined in a database table, with fields defined in another. Each content item in the CMS would load itself, then its boxes then the entries in the boxes. Each of those content items would also have to load a content type instance from the db, and each of the fields a 'field prototype' too.

Code that altered the behaviour of each instance was loaded from both the content type and the content instance tables and evaled using Active Record's `after_initialize` hook. It was this feature that really made the system flexible & powerful. I could customize the behaviour of both individual pages and whole classes of pages by injecting code into their configuration. It looks awful written down but it really worked.

But, I had swallowed the "metadata in the database" pill all the way down and it eventually started to choke both me & the system.

What I realised was that **having your metadata in the database is a bad idea**. For at least the following reasons:

* **You not only have to write & support the editing interface, you have to write & support the content type admin interface.**

	Interfaces are hard. Supporting browsers is hard. You don't want to do it any more than you have to. If for every new feature you want to add to your type system, you have to write an accompanying HTML, CSS and JS interface your pace of development is going to suffer.

* **Developers should be able to use their preferred tool: the text editor.**

	If you're asking developers to write HTML code in the browser then you're stopping them from using their tools and **slowing them down**. If you're allowing them to use their text editor to develop templates but forcing them to use the browser to simultaneously develop their content types then you're **slowing them down**. Developers are at their most comfortable and most productive when working with text, with code. Browsers are feel slow, even running locally, even with AJAX. Mice are slow.
* **If your metadata is in the database, it can't be under version control.**

	You wouldn't write code without committing it to version control (right?!), so why are you happy to configure your CMS without version control? Content belongs in a database, configuration doesn't.

* **Updating the metadata interferes with updating the content.**

	This is perhaps the most important reason metadata in the db is bad. How do you update the metadata when your clients are constantly editing the site? With my "version 3" (which is really Spontaneous v1) I was constantly faced with this, and have been faced with it with every other CMS system I have used. If the client asks for a significant new feature then I need to develop that feature locally so that if anything goes wrong the live site is safe. Assuming that the development goes well and I am ready to make the new features live, how do I do it? I can't just upload my modified database because changes have been made in between me downloading my development version and finishing the new feature. Perhaps I asked the site editors to stop work for the duration of my development. I hope it didn't take too long in that case. No, the only workable solution is to develop your feature and as part of that, record a set of actions that you can reproduce on the live server. This becomes extremely tedious, extremely quickly.

So, when it came time to think about attempt 4, I knew what I had to do: I had to move the 'code injection' feature of attempt 3 from the level of an uncomfortable hack to a first-order feature. I had to move my metadata out of the database and into code.

## Attempt 4: Spontaneous CMS

After 6 years of having access to the fantastic tool that is "version 3", after 6 years of being able to build sophisticated & beautiful websites and almost never have a request for help with the editing interface, I thought it was time to try again.

In the intervening 6 years the Ruby toolset has grown enormously and browsers are an order of magnitude more capable & reliable. This time, I thought, I can really get it right.

And I think I have.

Or let's say at least I'm moving & pointed in the right direction.

My first versions of the code-based content type system used extensive DSLs to define fields but as I developed them I realised I was just reproducing features (such as inheritance) that already existed. When I realised I could just make these content types good-olde Ruby classes it was like the clouds parting.

After that and a couple of complete re-writes the system is starting to feel as intuitive and powerful as I hoped. There are still some rough edges and my list headed "needs re-writing" never seems to get shorter but I think I've solved the hardest problems already and the rest is just a question of tidying up after the party.


I can sit down and write a content schema in minutes. For existing sites I can make alterations, commit the changes to Git and then deploy the new version without touching the database. I can construct complex relations between types using common Ruby idioms such as `#map` and `#select`. I can build content-managed web sites that have the complexity & richness of a framework driven application without a single database migration or a single line of interface code. I again feel like I've created a system that's deeper than I can see and that I am going to have to learn how to use all over again, and that's a fantastic feeling.

## The Future

Instead of a a monolithic framework such as Rails I've instead opted for a selection of fantastic and highly focussed components. Instead of Active Record I'm using Sequel, all the web application code is handled by Sinatra and the whold CMS is just a fancy Rack application.

The aim is to be a large but tightly focussed system. I don't want  the core to do too much, I just want it to do enough. The reason it's taken so long to get here is that for a modern CMS 'enough' is quite a lot. On top of basic content editing features you need asset minification, cloud integration and an easy & reliable bootstrap experience to name but a few.

So altough the basics have been working for a while, it's taken much longer to pull everything together into a form that I would even consider writing documentation for. In fact every time I write documentation I end up adding new features because as I write down how I think it should work I realise that it doesn't actually work like that. So I have to add in the feature I just wrote about and it does tend to slow things down. It's difficult being a perfectionist.

As things stand, the glaring omission is documentation. Writing code is much more fun than documentation, but I know that without just a little bit of hand-holding even a system designed to be easy to use is hopelessly opaque to a new user.

Having said that, Spontaneous is not intended to be a CMS for people who've never coded before. The "No code needed! Just upload & run!" space is very full and it's the limitations of that model that have led to a shortage of options for people who do know how to code and have greater ambitions than can be satisfied by a system hobbled by its insistence on browser based user interfaces (I would assert that it's harder to learn & navigate a new user interface than write code using a new API).



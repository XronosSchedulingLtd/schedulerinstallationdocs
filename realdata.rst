.. _real_data:

Real data
=========

Authentication
--------------

As soon as you think about setting up real data in your Scheduler
system, you have to start thinking about authentication - the process
of your users identifying themselves to the system.

In demo mode, no authentication is necessary - you can log in as
two different teachers, or as one student, just by selecting the user
you want from a pop-down menu.

As soon as you set up a system which isn't in demo mode, this option
disappears - you need to authenticate yourself properly.

Scheduler makes use of
`OmniAuth <https://github.com/omniauth/omniauth>`_, a flexible authentication
module with a lot of different possible methods of authentication.

However, all the schools using Scheduler so far have used Google for
their authentication, so the only option available so far is Google's oauth2.
If you need something different, do get in touch - info@xronos.uk

These instructions cover how to set up your Scheduler system to use
Google's authentication.


Seed data
---------

The Scheduler application comes with seed data which sets your system
up to look just like the
`Scheduler demonstration site <https://schedulerdemo.xronos.uk/>`_.

This is handy for initial proof that your installation works, but
you need a clean system into which to put your real data.

Edit the file db/seeds.rb and find the following lines

::

  seeder = Seeder.new("schedulerdemo.xronos.uk")

  #
  #  First, some which are intrinsic to the functioning of the system.
  #
  seeder.create_essentials

  #
  #  And the rest fall under a heading of likely to be useful.
  #
  seeder.create_usefuls

  #============================================================================
  #
  #  Everything from here on down is demonstration data.  You probably don't
  #  want to load it in a new live system.
  #
  #============================================================================

Change "schedulerdemo.xronos.uk" to the domain name of your server,
and then delete the final comment and everything which follows it.

If you now re-run the seeding process, you will have a completely
blank system, ready for your data - don't do that yet.


Initial user
------------

You're going to need to be able to log on to your system, which means
you need an initial user.

Scheduler is potentially able to use a wide variety of authentication
mechanisms, but at present the only one which is supported is Google Auth.
(Do get in touch - info@xronos.uk - if you need an alternative.)

When a user logs in using Google Auth, Scheduler checks to see whether it
recognizes that user as a member of staff or pupil.  If it does, the
new user gets set up with appropriate access to the system.

We thus need at least one staff record in the system to be able to log on.

Still editing the same file - db/seeds.rb - add an extra line at
the end like this:

::

  seeder.new_staff("Mr", "Able", "Baker", "AB", [], "able.baker@gmail.com")

The fields here are as follows:

- Title
- Given name
- Family name
- Initials
- Array of subjects taught (don't worry about this)
- Email address

The crucial one is the e-mail address.  Scheduler will compare this
with what it gets back from Google Auth, and if the two match then it
will treat the user as a member of staff.  Obviously, use a real
person's name and Gmail address.

.. note::

  If you use Google authentication then anyone with a Google account
  can log in to your system.  Authorization however is a different
  step from Authentication.  If an arbitrary individual authenticates into
  your system, they won't have a matching staff or pupil record, and
  so will be treated as a guest - they will have no more access to the
  system than if they weren't logged in.

  When Mr Able Baker (set up above) logs in, the system will recognize
  him as a member of staff, and give him whatever permissions staff
  are configured as having.

With that in place you can then re-seed the database with:

::

  $ export RAILS_ENV=production          # If you want to do the production d/b
  $ rake db:schema:load
  $ rake db:seed

And you'll have a nice blank database ready to use with some fundamental
records already in place.


Google Auth
-----------





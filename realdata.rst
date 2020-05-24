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

  seeder = Seeder.new(
    public_title:    "Xronos Scheduler",
    internal_title:  "Scheduler - Lorem Ipsum Academy",
    dns_domain_name: "schedulerdemo.xronos.uk"
  )

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


You'll probably want to change the two titles to something more suitable
for your school.  The public one is shown to casual visitors, whilst
the internal one is shown to users who have logged in.

Typical examples might be "My School Calendar" for the public one,
and "Scheduler - My School", for the internal one.

Change "schedulerdemo.xronos.uk" to the domain name of your server,
and then delete the final comment and everything which follows it.

If you now re-run the seeding process, you will have a completely
blank system, ready for your data - don't do that yet.


Initial user
------------

You're going to need to be able to log on to your system, which means
you need an initial user.

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

To set up Google Authentication, you'll need an account with Google.  Visit
the `Google Developers Console <https://console.developers.google.com>`_ and
log in as your chosen user.

The first thing which you need to do is create a Project.  At the top
left of the screen, just to the right of "Google APIs" there is a pull-down
menu - currently empty.  Click on that, and then on the + button
to create a new project.

Give your project a name - e.g. Scheduler - and then save it.

Click on the pull-down menu again and choose Scheduler as your current
project.  A pop-up will appear, prompting you to create credentials.
Click on the "Create credentials" button and choose "OAuth client ID".

At this point you will then be told you need to configure your application's
consent screen.  Click on the "Configure consent screen" button and fill
in the "Product name shown to users" field.  You would probably want to
call it something like "<My school's name> Scheduler".  The idea is to make
it clear to users exactly what system they are logging in to.

Save that form and you'll be taken back the credentials creation process.
Choose "Web application" as the application type, give it a name (e.g.
"Scheduler" again) and then you need to provide a couple of URLs.

The first of these is the address that requests to Google will be
coming *from* - i.e. the basic URL of your web server.  Using our
example address that would be:

::

  https://scheduler.myschool.org.uk

The second field is to tell Google where to redirect users who have
completed the login process.  This would be:

::

  https://scheduler.myschool.org.uk/auth/google_oauth2/callback


You can put more than one value in each of these fields.  If you're
going to be doing development work on a local system you might want to
add also

::

  http://localhost

and

::

  http://localhost/auth/google_oauth2/callback


Similarly, if you want to be able to use real authentication before you
have set up https, you might want to add http: versions of the above.

Altogether, that would give you the following authorised origins:

::

  https://scheduler.myschool.org.uk
  http://scheduler.myschool.org.uk
  http://localhost

and the following authorised redirects.

::

  https://scheduler.myschool.org.uk/auth/google_oauth2/callback
  http://scheduler.myschool.org.uk/auth/google_oauth2/callback
  http://localhost/auth/google_oauth2/callback

Obviously, use your own domain name and not the sample one given here.

When you click the "Create" button, a fresh pop up window will appear
telling you the Client ID and Client Secret for your application.

Copy both of these into your ~/etc/authcredentials file which you
created earlier.  By default it looks like this:

::

  GOOGLE_CLIENT_ID=""
  GOOGLE_CLIENT_SECRET=""

Put the newly acquired items between the quotation marks.

To be able to pick them up when Scheduler is run from the command
line, edit the scheduler user's ~/.profile file and add the following
lines.

::

  . $HOME/etc/authcredentials
  export GOOGLE_CLIENT_ID GOOGLE_CLIENT_SECRET

If you're still running the application in development mode then you'll
need to log out and back in again for these to take effect.  If you're
running in production mode then restart the server with
"sudo service nginx restart".

You need to enable the "Contacts API" in the Google Developers Console
in order for authentication to work.

.. warning::

  It seems it can take a few minutes for your new credentials to
  propagate to Google's servers.  When you first create them, try
  waiting 5 minutes before attempting to log in to the Scheduler
  application.

You should now be able to log in to your Scheduler installation using
the gmail address of the staff member which you set up earlier.


Administrator access
--------------------

Having logged in as your new user, you should find that you are
recognized as a member of staff and can create events etc.

.. note::

  If you can log in (your name appears at the top right) but the
  rest of the screen looks just the same as it did before - no
  menus or anything - then there is probably a mismatch between the
  e-mail address which you gave your staff member above, and the
  e-mail address which you used to log in.  They must be exactly the
  same for Scheduler to recognize your user.

However, you really need an admin user - one who can change anything
within the system.  For now, this is a manual process.  Proceed as follows:

::

  $ cd ~/Work/Coding/scheduler
  $ export RAILS_ENV=production        # Or development
  $ rails c
  2.5.5 :001 > u = User.first
  ...                                  # Output suppressed
  2.5.5 :002 > u.permissions[:admin] = true
  ...
  2.5.5 :003 > u.save
  ...
  2.5.5 :004 > exit
  $

What you are doing here is to invoke the rails console, which gives you
direct access to the database and the means to type in Ruby code to be
executed immediately.

What you are doing here is to:

- Select the first (only) user in the system
- Give him or her admin privileges
- Save the record back to the database
- And exit

You should then find that your user has full admin privileges and
you can proceed to the Scheduler Configuration Guide.

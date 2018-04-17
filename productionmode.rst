.. _production_mode:

Production mode
===============

So far, you've set up Scheduler running in development mode.  This means
it is started from the command line, and runs on a non-privileged port
(3000 by default) on your computer.  It is also producing far more logging
information than you would want in a live installation.

To make it work as a proper server, you need a web server and the means
to get Scheduler to start up silently in the background each time
the server is re-booted.

If you still have Scheduler running in development mode, then shut it
down by typing Ctrl-C in the relevant terminal window.

IP address
----------

In order to be accessible, your server will need to be allocated
a stable IP address.  How this is done will depend on your organisation's
existing infrastructure.  If you are using a commercial Virtual Private
Server (VPS) then they should be able to tell you what your IP address is.


Domain name
-----------

Similarly, you will need a domain name for your server.  Typically this
will be something like "scheduler.myschool.org.uk".  Again, it will need
to be allocated by your organisation's IT administration, and pointed to
the IP address mentioned above.


Nginx / Passenger
-----------------

The preferred web server for Scheduler is Nginx, although you can also
run it with Apache if you prefer.  Instructions for using Apache are not
included here.

To run the application from Nginx, we recommend the use of
`Phusion Passenger <https://www.phusionpassenger.com/>`_.  Passenger is
tightly integrated with Nginx and the two are best installed together.
You can if you wish compile them yourself, but it's much easier to
use the ready-built packages provided by Phusion.

Phusion takes care of starting your application when necessary, giving
it exactly the environment in which to run - it runs it as your user,
and with the correct versions of Ruby and Rails.

There are separate installation instructions for
`Debian GNU/Linux 8.0 <https://www.phusionpassenger.com/library/install/nginx/install/oss/jessie/>`_ and
`Debian GNU/Linux 9.0 <https://www.phusionpassenger.com/library/install/nginx/install/oss/stretch/>`_.

.. warning::

  The instructions are subtly different.  For Debian 8, you install Nginx and
  Phusion Passenger all in one go by following the above instructions.  For
  Debian 9, you must install nginx *first*, then follow the above instructions
  to add Phusion Passenger.

Follow those instructions to get both Nginx and Passenger installed on
your system.


Configure Scheduler instance
----------------------------

Once Nginx and Passenger are installed and running, you can configure them
to invoke your Scheduler installation.  The following files assume your
Scheduler user is called "scheduler" and the domain name for your
installation is "scheduler.myschool.org.uk".

Passenger provides a helpful utility to check exactly how to set up
the right version of Ruby for your instance of Scheduler.

::

  $ cd ~/Work/Coding/scheduler
  $ passenger-config about ruby-command
  passenger-config was invoked through the following Ruby interpreter:
    Command: /home/scheduler/.rvm/gems/ruby-2.3.6@scheduler/wrappers/ruby
    Version: ruby 2.3.6p384 (2017-12-14 revision 61254) [x86_64-linux]
    To use in Apache: PassengerRuby /home/scheduler/.rvm/gems/ruby-2.3.6@scheduler/wrappers/ruby
    To use in Nginx : passenger_ruby /home/scheduler/.rvm/gems/ruby-2.3.6@scheduler/wrappers/ruby
    To use with Standalone: /home/scheduler/.rvm/gems/ruby-2.3.6@scheduler/wrappers/ruby /usr/bin/passenger start


  ## Notes for RVM users
  Do you want to know which command to use for a different Ruby interpreter?
  'rvm use' that Ruby interpreter, then re-run 'passenger-config about
  ruby-command'.
  $ 


The line in which we're interested is the one which begins "To use in Nginx..".

Create a new file - /etc/nginx/sites-available/scheduler - with the following
contents.

::

  server {
        listen 80;
        server_name scheduler.myschool.org.uk;
        root /home/scheduler/Work/Coding/scheduler/public;
        location ~ /.well-known {
                allow all;
        }
        passenger_enabled on;
        passenger_ruby /home/scheduler/.rvm/gems/ruby-2.3.6@scheduler/wrappers/ruby;
  }

Note the use of the domain name and user name specified previously.
Adjust these to suit your particular installation.

One thing may strike you as odd here - the directory specified above
as being the root directory of the application does not yet exist. This
is where one of the differences between the development and production
environments starts to become apparent.

When you're running the application in development mode, all the necessary
JavaScript and CSS files are downloaded separately to your browser.  That
way if you change one, the change can be reflected immediately.  Some of
the JavaScript files are actually written in CoffeeScript, which is compiled
on the fly as it's sent to the browser.

In the production environment, these items are assumed to be static, and
you want them transferred to the browser only once, as efficiently as
possible.  For this reason, all the files are pre-prepared and packed
into one large JavaScript file and one large CSS file.  These are then
served directly by Nginx, removing the need for the application to be
involved at all.  Happily the process is fully automated.  To prepare
these files (and incidentally, create your public directory) go into
the application directory and create them as follows:

::

  $ cd ~/Work/Coding/scheduler
  $ RAILS_ENV=production rake assets:precompile

The production copy uses its own separate database, which needs populating
too.

::

  $ RAILS_ENV=production rake db:schema:load
  $ RAILS_ENV=production rake db:seed

And finally, you need to set up a secret key for the application to use.
These are hard-coded for development mode, but you need one unique to your
system to use in production.  Happily, Rails comes with a tool which
will manufacture one for you.

::

  $ rake secret
  2430ed4ee29d085891d17083d7395a4f6a0042c0b0e1de2c734d3e21bd8dfad7ea8158ad57cbfec3183830b5218c3a97215d22040aedd0e02536a4acb9389f0f
  $

Don't use that one - it's not secret now.  Generate your own.  You need to
put it at the end of your user's ~/.profile file, like this:

::

  SECRET_KEY_BASE="2430ed4ee29d085891d17083d7395a4f6a0042c0b0e1de2c734d3e21bd8dfad7ea8158ad57cbfec3183830b5218c3a97215d22040aedd0e02536a4acb9389f0f"
  export SECRET_KEY_BASE

You're now ready to run up your application in production mode.

Create a symbolic link in /etc/nginx/sites-enabled to the configuration
file created earlier with:

::

  $ sudo ln -s /etc/nginx/sites-available/scheduler /etc/nginx/sites-enabled/scheduler


and then restart Nginx with:

::

  $ sudo service nginx restart

You should then be able to access the application by pointing your
web browser at http://<your domain name>/

.. note::

  Sometimes Nginx fails to start at this point with an error message,
  "could not build server_names_hash".  If this happens to you, edit
  /etc/nginx/nginx.conf and uncomment the line there which reads:

  ::
  
    # server_names_hash_bucket_size 64

  This seems to cure the problem.


Because you have now set up Scheduler to run as a service under Nginx
and Phusion Passenger, it will all start up automatically each time
your server is booted.  As part of the boot process, Nginx is started,
which in turn starts Passenger, which starts up instances of Scheduler
as needed.

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

Overview
--------

The preferred configuration for Scheduler in a production environment is
to use Puma as the application server, running behind Nginx which functions
as the web server.

Requests arriving from clients go first to Nginx which handles the ones
which it can (requests for static resources - JavaScript files, stylesheets
and images) and passes on the rest to Puma.  Puma runs the Scheduler
application.


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

Scheduler preparation
---------------------

A little more preparation is needed for Scheduler to run in production
mode.  When it's running in development mode, resources are compiled
on the fly and sent to the web browser.  This means that things can be
changed and the change takes place immediately, without even restarting
the Scheduler process.

In production mode, things aren't meant to change, so they're prepared
in advance and then sent out directly by Nginx.

To switch your system to running production mode by default, edit
the file ~/etc/whichsystem and change the RAILS_ENV environment from a
value of "development" to a value of "production".

At the same time make sure the SCHEDULER_DIR variable in the same file
is set to where you have installed Scheduler.

Then make those settings effective in your current session by
typing:

::

  . ~/etc/whichsystem

Note the full stop at the start of that line.

Next you need to pre-compile all the static resources of your application.
Type the following:

::

  $ cd $SCHEDULER_DIR
  $ rake assets:precompile

If you haven't already set up your production database (depending on whether
you have done the :ref:`real_data` step) then create a production database
with:

::

  $ rake db:schema:load
  $ rake db:seed

Scheduler is now ready to run in production mode.


Puma
----

Puma is a Ruby Gem and it has already been installed as part of your
installation of Scheduler.  When you started Scheduler in development mode
what you actually started was Puma, which in turn started up your application.

For production mode, what is needed is for Puma to start automatically
as soon as your system boots.

Take a look at the file config/puma-live.rb  This contains Puma's
configuration for live running.  In particular, you might want to adjust
the first setting - "workers 4" - to match the number of CPU cores in
your system.

Copy the file support/puma.service to /etc/systemd/system:

::

  $ sudo cp support/puma.service /etc/systemd/system

This gives the system the information which it needs to start up
Puma and thus Scheduler.  Now it's just a case of telling systemd
that we want Puma to start:

::

  $ sudo systemctl enable puma.service
  $ sudo systemctl start puma.service

The first line tells systemd that in general you want puma to start
when the system boots, and the second one tells it to start puma *now*.

You can check that puma is running by looking in the directory
shared/logs where you should find two log files which it has created.

Nginx
-----

The preferred web server for Scheduler is Nginx, although you can also
run it with Apache if you prefer.  Instructions for using Apache are not
included here.

Nginx can be installed easily from the standard Debian repositories
with the command:

::

  $ sudo apt install nginx

Once that command completes, you will have Nginx running on your system.

Next you need to tell Nginx how to pass requests on to Puma.  Again,
a suitable configuration file is provided in the support directory.

Edit the file support/scheduler to set:

* Your server's domain name
* The full path of where you have installed Scheduler (two places)

The relevant bit of the file looks like this:

::

  #
  #  This tells Nginx where to find our application server.
  #
  upstream scheduler {
    server unix:///home/scheduler/Work/Coding/scheduler/shared/sockets/scheduler.sock;
  }

  #
  #  Front-end our scheduler server processes
  #
  server {
    server_name scheduler.myschool.org.uk;
    listen 80;
    listen [::]:80;
    root /home/scheduler/Work/Coding/scheduler/public;

    ...
  }

If you haven't changed Scheduler's installation directory, then you need
change only the server_name in this file.  Once you've edited it,
install it with:

::

  $ sudo cp support/scheduler /etc/nginx/sites-available
  $ sudo ln -s /etc/nginx/sites-available/scheduler /etc/nginx/sites-enabled/scheduler

and then restart Nginx with:

::

  $ sudo service nginx restart

You should then be able to access the application by pointing your
web browser at http://<your domain name>/


Log rotation
------------

Because Puma and thus Scheduler are now running permanently in the background
their log files will grow monotonically.  Debian GNU/Linux includes a
program called logrotate which can rotate such log files periodically
provided it is told about them.

If you have changed the Scheduler installation directory then edit the
file support/logrotate.scheduler to specify your new directory, then
install it with:

::

  $ sudo cp support/logrotate.scheduler /etc/logrotate.d/scheduler



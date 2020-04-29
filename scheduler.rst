Scheduler
=========

Now you've set up all the necessary pre-requisites, you can install
the Scheduler application itself.

.. note::

  There is nothing to stop you fetching the Scheduler code first if you
  just want to have a look at the source etc.  You won't however be
  able to run the application (or do the "bundle install" step below)
  until you have all the pre-requisites in place.


Download
--------

The Scheduler source code resides on
`Github <https://github.com/>`_.

Before you download it, think about where you're going to put the files
in your user's home directory.  All the development work is done using
a directory name of:

  ~/Work/Coding/scheduler

so unless you have a good reason not to, that might be a good place
for it.

::

  $ cd
  $ mkdir -p Work/Coding
  $ cd Work/Coding
  $ git clone https://github.com/XronosSchedulingLtd/scheduler.git
  $ cd scheduler
  $ gem install bundler
  $ bundle install

These commands create the necessary directory structure, fetch the
application files from Github, and then finally fetch and install
the necessary support software.

There's an interesting bootstrap problem.  All the necessary support
packages are listed in a file called Gemfile.lock within the scheduler
directory.  One of these support packages is called "bundler", but that
package needs to be installed in order to be able to use it to install
all the others.  Hence we manually install the bundler gem, then use it
to install the rest.


Create database
---------------

Before you can run up the application, you need some databases to
be created within MariaDB.  A script is provided to assist
in setting these up.  Under the scheduler directory it will be
found as:

  support/setupmysql

Edit the script and you will see an environment variable to set.

::

  DATABASE_PASSWORD="not set"

The password is up to you but make it something secure.  Then run the
script with

::

  $ sudo support/setupmysql

The sudo part of this command line will prompt you for your user's
password.

Edit the file:

  config/database.yml

and set the password there to the same as you chose to put in the
DATABASE_PASSWORD variable.

You can then create the database tables and give them some initial
sample data with the following commands.

::

  $ rake db:schema:load
  $ rake db:seed


Run it
------

And now you should be in a position to run up the application in
demonstration mode with the following command:

::

  $ rails s -b 0.0.0.0

Point a web browser to http://<your host>:3000 and you should see
the Scheduler demonstration site.  The application is running in development
mode with a copy of the data used on the demonstration site.

.. note::

  The "-b 0.0.0.0" bit is needed because without it the rails server
  listens only on address 127.0.0.1.  This is fine if you're running
  your web browser on the same machine as the rails application, but fails
  when you're setting up a headless server.

You can log in as one of the three demonstration users using the menu
at the top right.

Before you go - one last thing to set up.  All the utilities and particularly
the cron jobs within the system need to know which version of Ruby and
which gemset to use.  Rather than editing them all to
specify "ruby-2.5.5@scheduler" they expect an alias to be in place.
Do the following:

::

  $ rvm alias create scheduler ruby-2.5.5@scheduler


.. warning::

  Don't miss that last step.  Without it, the application will fail
  to start in production mode and your cron jobs will fail.

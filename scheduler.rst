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
  $ bundle install

These commands create the necessary directory structure, fetch the
application files from Github, and then finally fetch and install
the necessary support software.


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
  your web browser on the same machine as the rails application, and
  can thus access it at http://localhost:3000 but fails
  when you're setting up a headless server and need to access it from
  a web browser running on a different machine.

You can log in as one of the three demonstration users using the menu
at the top right.

A little extra
--------------

You now have the application running in development mode but before you go,
there are a few more things to set up.

Credentials
***********

For security, Rails applications use some secret random strings
to make sure that no-one can break in on the sessions between server
and web browser.  These aren't provided as part of the Scheduler distribution
because they need to be unique to your system.  Create them with
the following command:

::

  $ EDITOR=vi rails credentials:edit

(Set EDITOR to your preferred editor if it isn't vi.)

As there are currently no credentials set up on your system a new
set will be created for you and displayed in the editor.  Simply save
the file, exit the editor and all will be set up.

The credentials will be stored encrypted in a file config/credentials.yml.enc
and the encryption key will be saved in config/master.key.

Ruby environment
****************

All the utilities and particularly
the cron jobs within the system need to know which version of Ruby and
which gemset to use.  Rather than editing them all to
specify "ruby-2.6.6@scheduler" they expect an alias to be in place.
Do the following:

::

  $ rvm alias create scheduler ruby-2.6.6@scheduler


Environment variables
*********************

There are also a couple of files which will contain environment variables
to aid in running the application and associated jobs.  These live in
an etc directory under your Scheduler user's home directory.  Copy them
as follows:

::

  $ mkdir ~/etc
  $ cp support/whichsystem ~/etc
  $ cp support/authcredentials ~/etc

You will configure the contents of these files a little more later.

.. warning::

  Don't miss these steps.  Without them, the application will fail
  to start in production mode and your cron jobs will fail.

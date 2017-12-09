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

Before you can run up the application, you need some






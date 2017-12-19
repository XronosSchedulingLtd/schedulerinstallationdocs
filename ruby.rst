Ruby
====

.. warning::

  All the following should be done as your chosen user - scheduler - not
  as root.

  If you are still logged in as root (your command line prompt is a # symbol)
  then log out now and log back in as scheduler.

Ruby Version Manager (RVM)
--------------------------

In order to make sure we are using a compatible set of packages, we
use the
`Ruby Version Manager (RVM) <https://rvm.io>`_ to install and manage them.

By using RVM, one can have multiple copies of Ruby, Rails and supporting
packages installed on the same machine, and be sure of always using
exactly the one intended.

You will find instructions for installing RVM on the page referenced
above, but in essence it comes down to just two commands.

::

  $ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
  $ \curl -sSL https://get.rvm.io | bash -s stable

This incidentally is why you installed curl in the previous step.

.. note::

  It is just possible that the gpg command above will fail, apparently due to
  a compatibility problem between dirmngr and some DNS servers.  It's not a
  common occurence, but it has been known to happen.  If it happens to you,
  there is a workaround.

  ::

    $ echo "standard-resolver" >>~/.gnupg/dirmngr.conf
    $ killall dirmngr

  and then try again.

Once the installation has completed, log out and then back in again in
order to pick up your modified environment.

Ruby
----

The application currently uses Ruby version 2.3.6 - at the time of
writing the latest point release in the 2.3 branch.  It has not yet
been fully tested using Ruby 2.4.

To install the necessary Ruby, use the following command.  It can
take a few minutes to complete, depending on the speed of your
processor.

::

  $ rvm install ruby-2.3.6

You will be prompted for your user's password (not the root
password) in order to install extra required operating system packages.

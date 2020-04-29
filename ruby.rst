Ruby
====

.. warning::

  All the following should be done as your chosen user - scheduler - not
  as root.

  If you are still logged in as root (your command line prompt is a # symbol)
  then log out now and log back in as scheduler.  You'll note that your
  command line prompt changes to a $ symbol.

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

  $ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
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

.. note::

  If you are doing all this not on a headless server but on a workstation
  with the default Debian Gnome desktop, there's one little extra
  wrinkle.

  To get rvm to work properly you need to edit your terminal
  preferences and under the "Command" tab, tick "Run command as a login
  shell".  If you don't do that then rvm doesn't get to initialize itself
  properly when you start up an interactive terminal.

Ruby
----

The application currently uses Ruby version 2.5.5.  The 2.6 branch
has been released, but at the time of writing is still quite young.

To install the necessary Ruby, use the following command.  It can
take a few minutes to complete, depending on the speed of your
processor.

::

  $ rvm install ruby-2.5.5

You will be prompted for your user's password (not the root
password) in order to install extra required operating system packages.

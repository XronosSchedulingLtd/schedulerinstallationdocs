Operating System
================

Xronos Scheduler has been tested on both the current version
of Debian GNU/Linux (version 9 - Stretch) and on the previous
one (version 8 - Jessie).  It's actually slightly easier to
install it on version 8, because that version includes packages for MySQL
by default, whilst in version 9 it has been replaced by MariaDB.

MariaDB is meant to be a drop-in replacement for MySQL, but it
has sufficient differences to render its use problematic.  For
now, you are recommended to stick with MySQL.  Happily, MySQL
packages are still available to suit Debian GNU/Linux version 9,
and they're not difficult to add in.


Download
--------

Download the "Small CDs or USB sticks" image for amd64 (or i386 if you
have only a 32-bit processor) for either
`version 8 <https://www.debian.org/releases/jessie/debian-installer/>`_
or
`version 9 <https://www.debian.org/distrib/netinst>`_.

.. note::

  If you are doing a fresh installation of Debian GNU/Linux onto a
  normal PC or virtual machine, then you almost certainly want to choose
  the "amd64" version of the installer.  This is the 64 bit version for
  both Intel and AMD processors.  Use the "i386" version only for old
  processors which are restricted to 32 bit.


Install
-------

If you are using a physical machine for your installation, you will
need to write the image to a CD or USB stick.  If you installing to
a Virtual Machine, you should be able to use the image file directly
as an emulated device.

Boot your machine (or VM) with the installation image and follow
the instructions.  At each choice, take the default offered except
at the stage of choosing the packages to install.  At that point,
un-tick "Debian Desktop Environment" and "Web server"
and tick "SSH Server".  There's
no point in having a graphical desktop environment on a headless server -
it's a waste of resources - but you will need to have an SSH server
running in order to be able to connect to it.

Many VM providers will bootstrap your VM for you with the operating
system of your choice.  In this case, just ask for the current
Debian GNU/Linux.

Initial packages
----------------

Once you have a running system, you will need some basic extra packages
and a user account under which to run the application.

It is assumed you have root access to the system and are going to create
a user called "scheduler" to run the software.

.. note::
  You can call the user anything you want, but don't try to run the
  application as root.  These instructions assume that the user will
  be called "scheduler".

As root, do the following:

::

  # apt-get install sudo vim curl git libicu-dev memcached dirmngr
  # adduser scheduler
  # adduser scheduler sudo
  # adduser scheduler staff

These lines install the necessary packages, add the scheduler user,
and then put that user in a couple of necessary groups.

Log out as root and log back in as your chosen user.

.. note::

  The following instructions frequently ask you to edit a file.  To
  do this you need some sort of text editor, and as this is a headless
  server, it's going to need to be a text-mode one, not a graphical one.

  You may have noticed that vim was installed as one of the basic packages,
  so if you're happy with vim then do use that.  It's a very powerful
  editor, but perhaps has a bit of a steep initial learning curve.  For
  small edits, and if you don't already have a preference for a particular
  editor, "nano" is an editor which is very easy to get started with.

  If you'd like to learn vim, you'll find an interactive tutorial on your
  system which can be invoked with the command "vimtutor".

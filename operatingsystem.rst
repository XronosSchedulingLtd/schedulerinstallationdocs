Operating System
================

Download
--------

Download the "Small CDs or USB sticks" image for amd64 (or i386 if you
have only a 32-bit processor) from
`here <https://www.debian.org/distrib/netinst>`_.

Install
-------

If you are using a physical machine for your installation, you will
need to write the image to a CD or USB stick.  If you installing to
a Virtual Machine, you should be able to use the image file directly
as an emulated device.

Boot your machine (or VM) with the installation image and follow
the instructions.  At each choice, take the default offered except
at the stage of choosing the packages to install.  At that point,
un-tick "Debian Desktop Environment" and tick "SSH Server".  There's
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

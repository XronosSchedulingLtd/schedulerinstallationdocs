.. Xronos Scheduler documentation master file, created by
   sphinx-quickstart on Thu Nov 17 11:45:33 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Index
=====

.. image:: public.png
   :scale: 35%
   :align: right

Xronos Scheduler - installation instructions.

Xronos Scheduler is a web application, written using the
`Ruby on Rails <http://rubyonrails.org>`_ web application framework.
As such, it can be deployed to any of the many hosting services which
provide support for such applications.

These instructions are intended for those who want instead to set up
a suitable hosting platform for themselves - either a dedicated
physical machine, or some kind of virtual server.

In these circumstances, the recommended operating system is
`Debian GNU/Linux <https://www.debian.org>`_, version 9 - Stretch.
It should be relatively straightforward to use any other version
of Linux, or MacOS.

You may already have your own web application infrastructure, in which
case you should be able to slot Scheduler into it.

These instructions assume you are using a fresh, blank installation
of Debian GNU/Linux version 9, with just the bare operating system installed.

.. note::

  If you are doing a fresh installation of Debian GNU/Linux onto a
  normal PC or virtual machine, then you almost certainly want to choose
  the "amd64" version of the installer.  This is the 64 bit version for
  both Intel and AMD processors.  Use the "i386" version only for old
  processors which are restricted to 32 bit.

Installation images for Debian GNU/Linux are available
`here <https://www.debian.org/distrib/netinst>`_.

.. toctree::

   hardware
   operatingsystem
   ruby
   mysql
   nodejs
   scheduler


Scheduler is licensed under the GNU General Public Licence, version 2.


Indices and tables
==================

* :ref:`search`


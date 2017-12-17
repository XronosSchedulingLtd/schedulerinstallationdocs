MySQL
=====

Currently the application uses MySQL as its database server, so install MySQL.
The steps needed to do this depend on whether you are using version 8
or version 9 of Debian GNU/Linux.

Version 9 - Stretch
-------------------

A couple of extra steps are needed in order to configure your system
to pick up actual packages for MySQL.

.. warning::

  Do not install the packages called "mysql..." from Debian itself.
  Despite the names, they will give you MariaDB, which has enough
  differences from MySQL to make it unsuitable.

Download the mysql-apt-config deb file from
`MySQL's community website <https://dev.mysql.com/downloads/repo/apt/>`_
and install it with:

::

  $ sudo dpkg -i mysql-apt-config_0.8.9-1_all.deb
  $ sudo apt-get update

Obviously, adjust the version number if you have downloaded a later version.
This will add the MySQL repositories to your Debian installation, and
update your index of available software.

Now finish off your MySQL installation by following the instructions
as for version 8.


Version 8 - Jessie
------------------

::

  $ sudo apt-get install mysql-server mysql-client libmysqlclient-dev

During the installation, you will be prompted for a password for the
MySQL root user.  Make this something different from both your user's
password and the system root password.  Save it carefully, because you
will need it later.


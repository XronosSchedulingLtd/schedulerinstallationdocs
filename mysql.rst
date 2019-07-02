MySQL
=====

Currently the application uses MySQL as its database server, so install MySQL.
This is no longer included in Debian GNU/Linux 9 so a couple of extra
steps are needed.

.. warning::

  Do not install the packages called "mysql..." from Debian itself.
  Despite the names, they will give you MariaDB, which has enough
  differences from MySQL to make it unsuitable.

Download the mysql-apt-config deb file from
`MySQL's community website <https://dev.mysql.com/downloads/repo/apt/>`_
and install it with:

::

  $ wget https://dev.mysql.com/get/mysql-apt-config_0.8.12-1_all.deb
  $ sudo dpkg -i mysql-apt-config_0.8.12-1_all.deb
  $ sudo apt-get update
  $ sudo apt-get install mysql-server mysql-client libmysqlclient-dev

Check the above web page first to see whether this is still the current
version of the mysql-apt-config package.

When you install this package you will be asked what MySQL packages you
want to install - make sure you install version 5.7 and not the
default version 8.0.  The later version introduces incompatibilities
which will prevent the rest of the installation from running smoothly.



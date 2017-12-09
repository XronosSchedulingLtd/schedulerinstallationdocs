MySQL
=====

Currently the application uses MySQL as its database server, so install MySQL.

::

  $ sudo apt-get install mysql-server mysql-client default-libmysqlclient-dev

.. note::

  It seems that, as of Debian GNU/Linux 9.0, the package provided by
  the operating system is not MySQL, but MariaDB.  It is understood to
  be a drop-in replacement for MySQL.

In the course of the installation you will need to provide a superuser
password for mysql.  This should be different from both your ordinary
user's password, and from the system root password.  Keep a record of
it - you will need it later.


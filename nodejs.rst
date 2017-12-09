Node.JS
=======

`Node.JS <https://nodejs.org/en/>`_ is an implementation of JavaScript
which allows its use outside the original web browser environment.
Entire servers can now be written in JavaScript.

The Scheduler server is not written in JavaScript, but Rails does
make use of Node.JS to do some pre-processing of JavaScript which is
then sent to the user's browser.

The Debian GNU/Linux operating system does include a copy of Node
as one of its packages, but it's a very minimal installation and
lacks certain required parts.

Instead, use the Debian packages provided by the Node.JS project
itself.  Instructions are available
`here <https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions>`_ or simply do:

::

  $ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
  $ sudo apt-get install -y nodejs

The first command sets up your system so that it knows how to retrieve
packages from the Node.JS server, and then the second command does
the actual installation.

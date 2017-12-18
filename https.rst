Configure https
===============

Having got your installation up and running using unencrypted connections,
you probably want to go straight on and re-configure it to use https.
Increasingly the web is moving to using encrypted connections only, and
it's worth doing.

Let's Encrypt
-------------

In order to make use of encrypted connections, your web server will need
a digital certificate.  Your organisation may already have a supplier
for such certificates, but if not then you can get them free from the
`Let's Encrypt <https://letsencrypt.org/>`_ project.  The following
instructions assume you're going to use Let's Encrypt.

Install Certbot
---------------

The process of getting a certificate from Let's Encrypt is enabled by
a utility called certbot.  This is included in Debian 9, but you need
to do one extra step if you're using Debian 8.

For Debian 8 only, edit /etc/apt/sources.list and add the following line:

::

  deb http://ftp.uk.debian.org/debian/ jessie-backports main

then install certbot with the following commands:

::

  $ sudo apt-get update
  $ sudo apt-get install certbot -t jessie-backports

If you're using Debian 9, then all you need to do is:

::

  $ sudo apt-get install certbot


Request a certificate
---------------------

You should already have Nginx installed and running on your system
and accessible from the outside world, albeit only over http.  Before
Let's Encrypt will issue you with a certificate, they need to be
satisfied that you are actually have control of the machine making the
request, so their server will need to be able to contact yours to
validate the request.

It is assumed that you have set up your Nginix configuration as
specified on the previous page.  In particular, you must have
explicitly permitted access to the .well-known directory under your
web root.  The validation process will fail without this.

Issue the following command to request a certificate.  The first
time you use it you will be asked to agree to Let's Encrypt's terms
and conditions.

::

  $ sudo certbot certonly --webroot -w /home/scheduler/Work/Coding/scheduler/public -d <your server's domain name>

Provided the request succeeded, certbot will save all the parameters
to ease the renewal process later.

Use the certificate
-------------------

Having got a certificate for your server, the next stage is to configure
Nginx to make use of it.

Edit the file /etc/nging/sites-available/scheduler which you created
earlier and add the following server definition, *after* the existing
one.

::

  server {
        listen 443;
        server_name <your server's domain name>;
        ssl on;
        ssl_certificate      /etc/letsencrypt/live/<your server's domain name>/fullchain.pem;
        ssl_certificate_key  /etc/letsencrypt/live/<your server's domain name>/privkey.pem;

        ssl_session_timeout  60m;

        ssl_protocols  TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';
        ssl_prefer_server_ciphers   on;
        root /home/scheduler/Work/Coding/scheduler/public;
        passenger_enabled on;
        passenger_ruby /home/scheduler/.rvm/gems/ruby-2.3.6@scheduler/wrappers/ruby;
  }

Then restart nginx with:

::

  $ sudo service nginx restart

You should now be able to access your site as https://<your server's domain name>/


Prevent http access
-------------------

Once you have https access up and running, it makes sense to have that
as the default.  Users who type just "scheduler.myschool.org.uk" into their
browsers should be quietly re-directed to the encrypted version.

However, it is important to retain http access for Let's Encrypt, otherwise
you won't be able to renew your certificate when it expires.

Edit /etc/nginx/sites-available/scheduler again, and change the first
server definition to:

::

  server {
        listen 80;
        server_name <your server's domain name>;
        root /home/scheduler/Work/Coding/scheduler/public;
        location ~ /.well-known {
              allow all;
        }
        location / {
              return 301 https://$server_name$request_uri;
        }
  }

This tells Nginx to allow requests for the ".well-known" directory
(which Let's Encrypt uses to validate renewal requests) but to re-direct
any other requests to the same place on the https server.

Restart Nginx again with:

::

  $ sudo service nginx restart


Automate renewals
-----------------

Let's Encrypt's certificates are valid for only 90 days, so they need
to be renewed regularly.

The certbot utility should already have saved all the necessary information
to enable it to renew certificates.  You can test this quite easily
by typing:

::

  $ sudo certbot renew --dry-run

This won't actually renew your certificate, but it will check that all
is working.  Provided all is well, set up a cron job to run once a week
as root with the following command:

::
  
  certbot renew --post-hook "service nginx restart"

Although this job runs every week, it will attempt the renewal only
when the existing has less than 30 days of validity left.  The post-hook
will be run only after an actual renewal.


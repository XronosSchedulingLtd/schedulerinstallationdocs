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

Note that the certbot utility is still under active development, meaning
the process of using it gets steadily easier, but it makes trying to
write instructions for it a bit of a moving target.  Check the latest
instructions on the
`Certbot website <https://certbot.eff.org/>`_ if you have any difficulty.

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

  $ sudo apt-get install certbot python-certbot-nginx


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

Debian 8 - Jessie
-----------------

Issue the following command to request a certificate.  The first
time you use it you will be asked to agree to Let's Encrypt's terms
and conditions, and to provide a contact e-mail address for notifications.

::

  $ sudo certbot certonly --webroot -w /home/scheduler/Work/Coding/scheduler/public -d <your server's domain name>

Provided the request succeeded, certbot will save all the parameters
to ease the renewal process later.

Use the certificate
-------------------

Having got a certificate for your server, the next stage is to configure
Nginx to make use of it.

Edit the file /etc/nginx/sites-available/scheduler which you created
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
        passenger_ruby /home/scheduler/.rvm/gems/ruby-2.5.5@scheduler/wrappers/ruby;
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


Debian 9 - Stretch
------------------

The version of certbot packaged for Debian 9 is slightly more advanced
than the one available for Debian 8.  It can do more of the work for you.

The command to use is:

::

  $ sudo certbot --authenticator webroot --installer nginx

and it seems to do pretty much all of the work specified above for
the Jessie version.

It will prompt you for the webroot of your application, which is (assuming
you have followed the conventions given earlier):

  /home/scheduler/Work/Coding/scheduler/public

It will also ask you whether you want to restrict access to HTTPS only.
You are strongly recommended to say yes to this query.  It will then do
all the necessary edits to the Nginx configuration files for you.


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
is working.

The Debian certbot package sets up a cron job for you, which
will check your certificates and attempt renewal.  It needs a slight
tweak, because once the certificate has been renewed, we need to tell
Nginx about it.

As root (i.e. use sudo) edit the file /etc/cron.d/certbot and add

::

  --post-hook "service nginx restart"
  
to the end of the final line.

You probably also want to tweak the time
at which it is run.  The default setting is to run at midnight and noon
each day, which means it is just possible that your Nginx instance will
be re-started at noon about once every three to six months.

Probably better to run it once a day, outside normal business hours.
The time to run at is specified by the first two fields of the final
line of the file - by default "0 \*/12", meaning 0 minutes past any
hour divisible by 12.

If I decide instead to run the job at 5 minutes past 3 each morning,
I would change those two fields to read "5 3".

The final file would then look like this:

::

  # /etc/cron.d/certbot: crontab entries for the certbot package
  #
  # Upstream recommends attempting renewal twice a day
  #
  # Eventually, this will be an opportunity to validate certificates
  # haven't been revoked, etc.  Renewal will only occur if expiration
  # is within 30 days.
  SHELL=/bin/sh
  PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

  5 3 * * * root test -x /usr/bin/certbot -a \! -d /run/systemd/system && perl -e 'sleep int(rand(3600))' && certbot -q renew --post-hook "service nginx restart"


Although this job runs daily, it will attempt the renewal only
when the existing certificate has less than 30 days of validity left.
The post-hook will be run only after an actual renewal.


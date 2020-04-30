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
a utility called certbot.  This is included in Debian GNU/Linux.

To install it execute the following command:

::

  $ sudo apt install certbot python-certbot-nginx


Request a certificate
---------------------

You should already have Nginx installed and running on your system
and accessible from the outside world, albeit only over http.  Before
Let's Encrypt will issue you with a certificate, they need to be
satisfied that you are actually have control of the machine making the
request, so their server will need to be able to contact yours to
validate the request.

The command to use is:

::

  $ sudo certbot --nginx

You will be prompted for an e-mail address (so the certificate issuing
authority can contact you if one of your certificates hasn't been
renewed and is about to expire) and then to accept LetsEncrypt's terms
of service.

The utility will work out your configured domain name from the
Nginx configuration file which you set up earlier and then offer to
re-direct all requests to https only.  Take this option (option 2)
and it will modify the Nginx configuration file for your website
as needed.

Once the utility has finished you should find that any attempt to
access http://scheduler.myschool.org.uk (or whatever your site is
called) is automatically redirected to https://scheduler.myschool.org.uk.

The utility also sets up a background job which will automatically renew
the certificate before it expires.

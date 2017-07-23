Title: SSL with Let's Encrypt & Nginx on Debian Jessie
Date: 2017-07-21 22:00
Category: General
Tags: nginx, SSL

#SSL with Let's Encrypt & Nginx on Debian Jessie.

##About SSL
SSL (Secure Sockets Layer) or TLS (Transport Layer Security) are protocols that helps you to secure connection to your web-resource. TLS/SSL certificates enable encrypted HTTPS on web servers.
There are various ways to set HTTPS, but in this quick tutorial I will show you my workflow with [Let's Encrypt](https://letsencrypt.org/) on Debian Jessie (or any other debian linux distro, Ubuntu for example) with Nginx web server.

##About Let's encrypt
[Let's Encrypt](https://letsencrypt.org/) is a new Certificate Authority (CA) that provides an easy way to obtain and install free TLS/SSL certificates. If you want to obtain certificate from Let's Encrypt you will have to demonstrate that you have control over your domain.

To prove that domain is ours we will have to create folder dedicated for online challenge, configure Nginx to have access to that folder. So, when we run Let's Encrypt challenge it first creates a file in mentioned previously folder and then the AC part of Let's Encrypt will try remotely access that file on your server. If Nginx configured correct and points to right folder when AC () asks for access, you pass challenge and Let's encrypt create certificates for your domain name.

##Configuration of Let's Encrypt & Nginx

###Requirements
- SSH access to your web server
- Nginx [installed](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-debian-8)

###Let's encrypt installation and obtaining of certificates
1) Install let's encrypt code and prepare the environment:

    :::shell
    $ sudo apt-get install certbot -t jessie-backports

2) Create a folder for the online challenge where we prove that domain name belongs to us:

    :::shell
    sudo mkdir /srv/www/le/domain.name.com

3) Configure your Nginx to prepare for online challenge:

    :::shell
    server {
        listen 80;
        server_name domain.name.com;

        location '/.well-known/acme-challenge' {
            root /srv/www/le/domain.name.com/;
        }
    }

don't forger to reload your Nginx to enable new configurations

    :::shell
    $ sudo systemctl reload nginx.service

4) Run online challenge to create certificates

    :::shell
    $ sudo certbot certonly --webroot -w /srv/www/le/domian.name.com -d domian.name.com

in this command ```/srv/www/le/domian.name.com``` is a dedicated folder where Let's Encrypt palce file so AC can check it remotely and determine is domain is over our control.

If challenge went well you will see output similar to

    :::shell
    IMPORTANT NOTES:
    - Congratulations! Your certificate and chain have been saved at
    /etc/letsencrypt/live/domian.name.com/fullchain.pem. Your cert
    will expire on 2017-07-26. To obtain a new or tweaked version of
    this certificate in the future, simply run certbot again. To
    non-interactively renew *all* of your certificates, run "certbot
    renew"
    - If you lose your account credentials, you can recover through
    e-mails sent to sammy@example.com.
    - Your account credentials have been saved in your Certbot
    configuration directory at /etc/letsencrypt. You should make a
    secure backup of this folder now. This configuration directory will
    also contain certificates and private keys obtained by Certbot so
    making regular backups of this folder is ideal.
    - If you like Certbot, please consider supporting our work by:

    Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
    Donating to EFF:                    https://eff.org/donate-le


5) Configure SSL part in Nginx

First we need to redirect all HTTP requsts to HTTPS, by adding following to Nginx configs at HTTP part

    :::shell
    server {
        listen 80;
        server_name example.com;

        return 301 https://$server_name$request_uri;

        ...
    }

Second we need to add block for HTTPS to Nginx configs

    :::shell
    server {
        listen 443 ssl;
        server_name domain.name.com;

        include ssl_params;
        ssl_certificate /etc/letsencrypt/live/domain.name.com/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/domain.name.com/privkey.pem;
        ssl_trusted_certificate /etc/letsencrypt/live/domain.name.com/chain.pem;

        location '/.well-known/acme-challenge' {
            root /srv/www/le/domain.name.com/;
        }

        ....
    }

###Certificates renewal

To renew all the certificate, simply run:

    :::shell
    $ sudo certbot --force-renewal renew

To list certs due for renewal, run:

    :::shell
    $ sudo certbot --dry-run renew

To make life easier I have a cron at ```/etc/cron.d/sertbot```

    :::shell
    # /etc/cron.d/certbot: crontab entries for the certbot package
    #
    # Upstream recommends attempting renewal twice a day
    #
    # Eventually, this will be an opportunity to validate certificates
    # haven't been revoked, etc.  Renewal will only occur if expiration
    # is within 30 days.
    SHELL=/bin/sh
    PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

    0 */12 * * * root test -x /usr/bin/certbot -a \! -d /run/systemd/system && perl -e 'sleep int(rand(3600))' && certbot -q renew
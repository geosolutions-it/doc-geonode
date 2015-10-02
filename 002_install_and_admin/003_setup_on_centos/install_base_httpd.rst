
===============================
Apache HTTP Server Installation
===============================


Install Apache::

    yum install httpd

And additional modules::

    yum install httpd mod_ssl mod_proxy_html mod_wsgi

Firewall configuration
----------------------

Allow requests on port 80 through the firewall::

    firewall-cmd --zone=public --add-port=80/tcp --permanent
    firewall-cmd --zone=public --add-service=http --permanent
    firewall-cmd --reload

Security issues
---------------

There are a couple of security issues to fix when dealing with GeoNode.

GeoNode will run inside httpd through WSGI. This means that httpd will try to perform external connection toward the DB.
This is usually blocked by default by strict security policies, so we need to relax them::

   setsebool -P httpd_can_network_connect_db 1

The other issue is about SELinux itself: it is not WSGI friendly, so we'll have to disable it.
Edit the file ``/etc/sysconfig/selinux`` and  change the line::

   SELINUX=enforcing

into ::

   SELINUX=permissive

and reboot the machine.


httpd configuration
-------------------

As ``root`, create the file ``/etc/httpd/conf.d/geonode.conf`` 
and insert into it :download:`this content <resources/geonode.conf>`.

httpd may need access to some otherwise hidden files in ``geonode``'s home dir, so let's add
``apache`` to the ``geonode`` group::

   usermod -a -G geonode apache

Then restart httpd to make it reload the new configurations::

   systemctl restart httpd


To automatically start Apache at boot, run::

    systemctl enable httpd

Log Rotation
------------

Edit the configuration file for logrotate to rotate Apache log files
( /etc/logrotate.d/httpd ) as follows::

    /var/log/httpd/*log {
        daily
        maxsize 100M
        rotate 14
        missingok
        create 644 root root
        notifempty
        sharedscripts
        compress
        delaycompress
        postrotate
            /bin/systemctl reload httpd.service > /dev/null 2>/dev/null || true
        endscript
    }

And add the following line to the crontab::

    crontab -e

    0 * * * * /usr/sbin/logrotate /etc/logrotate.d/httpd

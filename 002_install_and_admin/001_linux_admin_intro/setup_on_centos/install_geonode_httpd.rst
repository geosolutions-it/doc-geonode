.. _geonode_setup_http:

###########
Setup httpd
###########

Security issues
===============

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
===================

As ``root`, create the file ``/etc/httpd/conf.d/geonode.conf`` 
and insert into it :download:`this content <resources/geonode.conf>`.

httpd may need access to some otherwise hidden files in ``geonode``'s home dir, so let's add
``apache`` to the ``geonode`` group::
 
   usermod -a -G geonode apache

Then restart httpd to make it reload the new configurations::

   systemctl restart httpd
   
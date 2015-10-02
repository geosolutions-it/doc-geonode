.. _geonode_install_geoserver:

#################
Install GeoServer
#################

We'll install GeoServer using the tomcat we already installed in :ref:`geonode_tomcat_install`.

Create tomcat dir
=================

Download the latest GeoServer release built with the GeoNode extensions::

   cd
   cd download
   wget http://build.geonode.org/geoserver/latest/geoserver.war

As ``root``, copy the ``base`` template into ``geoserver``::

   cp -a /var/lib/tomcat/base /var/lib/tomcat/geoserver
   chown tomcat: -R /var/lib/tomcat/geoserver

Copy the ``.war`` file into tomcat::   

   cp /root/download/geoserver.war /var/lib/tomcat/geoserver/webapps/
  
   mkdir /var/log/geoserver
   chown tomcat: /var/log/geoserver

Commands
========

Start GeoServer with::

   systemctl start  tomcat@geoserver

These are the commands for starting and stopping GeoServer:

- ``systemctl start  tomcat@geoserver``
- ``systemctl stop   tomcat@geoserver``
- ``systemctl status tomcat@geoserver``

Autostart
=========

The standard way for setting an autostarting service will not work (see https://bugzilla.redhat.com/show_bug.cgi?id=752774),
so this **will not** work ::

   systemctl enable tomcat@geoserver
   
This commmand seems to do the work::

   ln -s /etc/systemd/system/tomcat\@.service  /lib/systemd/system/multi-user.target.wants/tomcat\@geoserver.service


.. _geonode_install_geoserver_pw:
      
Change default password
=======================

Log into geoserver using the default credentials ``admin`` / ``geoserver``, and
change the default password.

The new password should also be set into GeoNode settings (see the entry ``OGC_SERVER / PASSWORD`` 
at :ref:`geonode_install_settings`).



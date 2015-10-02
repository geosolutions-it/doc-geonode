.. _install_geoserver_application:

=============================
Install GeoServer Application
=============================

In this section we are going to setup GeoServer for GeoNode. GeoServer will run inside
`Tomcat` sevrlet container.

Setup GeoServer
===============

You' ve already installed `Tomcat 7` in the system in the first section of the training.
Before you deploy GeoServer stop the running Tomcat instance:
::
    service tomcat7 stop

Now copy the downloaded GeoServer archive inside Tomcat's webapps folder:

.. note::
    As said previously in the training VM the actual path of GeoServer web archive is
    /home/geo/geoserver.war. If you are following the training you sould run this command instead::

        sudo cp /home/geo/geoserver.war /var/lib/tomcat7/webapps/

::
    sudo cp /home/geonode/geonode/downloaded/geoserver.war /var/lib/tomcat7/webapps/

Test GeoServer
===============

Now start Tomcat to deploy GeoServer
::
    service tomcat7 start

Tomcat will extract GeoServer web archive and start GeoServer. This may take some time

Open a web browser (in this example `Firefox`) and navigate to http://localhost/geoserver

.. image:: img/test_geoserver.png
   :width: 600px
   :alt: Connecto to GeoServer

In a few seconds GeoServer web interface will show up:

.. image:: img/test_geoserver2.png
   :width: 600px
   :alt: Connecto to GeoServer

GeoNode authentication integration
==================================

All we need to do now is to integrate GeoNode authentication so that GeoNode
administrator will b able to access and administer GeoServer as well.

Stop GeoServer
::
    sudo service tomcat7 stop

And navigate to `/var/lib/tomcat7/webapps/geoserver/WEB-INF/`
::
    cd /var/lib/tomcat7/webapps/geoserver/WEB-INF/

Edit `web.xml` with a text editor
::
    sudo gedit web.xml

 And add the following:
::
    <context-param>
        <param-name>GEONODE_BASE_URL</param-name>
        <param-value>http://localhost/</param-value>
    </context-param>

The resulting file should look like this:

::
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd">
    <web-app>
        <display-name>GeoServer</display-name>

      <context-param>
        <param-name>GEONODE_BASE_URL</param-name>
        <param-value>http://localhost/</param-value>
      </context-param>

      <context-param>
        <param-name>serviceStrategy</param-name>
        <!-- Meaning of the different values :

             PARTIAL-BUFFER2
             - Partially buffers the first xKb to disk. Once that has buffered, the the
               result is streamed to the user. This will allow for most errors to be caught
               early.
        ...

Restart GeoServer to make the changes effective
::
    sudo service tomcat7 start

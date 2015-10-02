.. _geonode_tomcat_install:

###################
Tomcat Installation
###################

Installing Java
===============

We'll need a JDK to run GeoServer.

You may already have the OpenJDK package (``java-1.7.0-openjdk-devel.x86_64``) installed.
Check and see if Java is already installed:: 

   # java -version
   java version "1.7.0_51"
   OpenJDK Runtime Environment (rhel-2.4.4.1.el6_5-x86_64 u51-b02)
   OpenJDK 64-Bit Server VM (build 24.45-b08, mixed mode)
   
   # javac -version
   javac 1.7.0_51       

If it is not, check for available versions::

   yum list *openjdk*
   
You'll get a list like this one, probably with versions 1.6.0, 1.7.0, 1.8.0::
   
   [...]
   java-1.6.0-openjdk.x86_64                                                                                                   1:1.6.0.0-3.1.13.1.el6_5                                                                                           @rhel-x86_64-server-6
   java-1.6.0-openjdk-devel.x86_64                                                                                             1:1.6.0.0-3.1.13.1.el6_5                                                                                           @rhel-x86_64-server-6
   java-1.6.0-openjdk-javadoc.x86_64                                                                                           1:1.6.0.0-3.1.13.1.el6_5                                                                                           @rhel-x86_64-server-6
   java-1.7.0-openjdk.x86_64                                                                                                   1:1.7.0.51-2.4.4.1.el6_5                                                                                           @rhel-x86_64-server-6
   java-1.7.0-openjdk-devel.x86_64                                                                                             1:1.7.0.51-2.4.4.1.el6_5                                                                                           @rhel-x86_64-server-6
   [...]
   
Go for the version 1.7.0::

   yum install java-1.7.0-openjdk-devel
   
Once done, the command ``java -version`` should return info about the installed version. 


Oracle JDK
----------

Until recently, the Oracle JDK was a better performer than the OpenJDK,
so it was the preferred choice. This is no longer true, anyway in the following paragraph you can find
instruction about how to install the Oracle JDK.

You can download the Oracle JDK RPM from this page:

  http://www.oracle.com/technetwork/java/javase/downloads/index.html

Oracle does not expose a URL to automatically dowload the JDK because an interactive licence acceptance is requested.  
You may start downloading the JDK RPM from a browser, and then either:

* stop the download from the browser and use on the server the dynamic download URL your browser has been assigned, or
* finish the download and transfer the JDK RPM to the server using ``scp``.   

Once you have the ``.rpm`` file, you can install it by::

  rpm -ivh jdk-7u51-linux-x64.rpm


Once installed, you still see that the default ``java`` and ``javac`` commands 
are still the ones from OpenJDK.
In order to switch JDK version you have to set the proper system `alternatives`.

You may want to refer to `this page <http://www.rackspace.com/knowledge_center/article/how-to-install-the-oracle-jdk-on-fedora-15-16>`_ .
Issue the command::

   alternatives --install /usr/bin/java java /usr/java/latest/bin/java 200000 \
   --slave /usr/lib/jvm/jre jre /usr/java/latest/jre \
   --slave /usr/lib/jvm-exports/jre jre_exports /usr/java/latest/jre/lib \
   --slave /usr/bin/keytool keytool /usr/java/latest/jre/bin/keytool \
   --slave /usr/bin/orbd orbd /usr/java/latest/jre/bin/orbd \
   --slave /usr/bin/pack200 pack200 /usr/java/latest/jre/bin/pack200 \
   --slave /usr/bin/rmid rmid /usr/java/latest/jre/bin/rmid \
   --slave /usr/bin/rmiregistry rmiregistry /usr/java/latest/jre/bin/rmiregistry \
   --slave /usr/bin/servertool servertool /usr/java/latest/jre/bin/servertool \
   --slave /usr/bin/tnameserv tnameserv /usr/java/latest/jre/bin/tnameserv \
   --slave /usr/bin/unpack200 unpack200 /usr/java/latest/jre/bin/unpack200 \
   --slave /usr/share/man/man1/java.1 java.1 /usr/java/latest/man/man1/java.1 \
   --slave /usr/share/man/man1/keytool.1 keytool.1 /usr/java/latest/man/man1/keytool.1 \
   --slave /usr/share/man/man1/orbd.1 orbd.1 /usr/java/latest/man/man1/orbd.1 \
   --slave /usr/share/man/man1/pack200.1 pack200.1 /usr/java/latest/man/man1/pack200.1 \
   --slave /usr/share/man/man1/rmid.1.gz rmid.1 /usr/java/latest/man/man1/rmid.1 \
   --slave /usr/share/man/man1/rmiregistry.1 rmiregistry.1 /usr/java/latest/man/man1/rmiregistry.1 \
   --slave /usr/share/man/man1/servertool.1 servertool.1 /usr/java/latest/man/man1/servertool.1 \
   --slave /usr/share/man/man1/tnameserv.1 tnameserv.1 /usr/java/latest/man/man1/tnameserv.1 \
   --slave /usr/share/man/man1/unpack200.1 unpack200.1 /usr/java/latest/man/man1/unpack200.1

Then run ::
  
   alternatives --config java
   
and select the number related to ``/usr/java/latest/bin/java``.

Now the default java version should be the Oracle one.
Verify the proper installation on the JDK::

  # java -version
  java version "1.7.0_51"
  Java(TM) SE Runtime Environment (build 1.7.0_51-b13)
  Java HotSpot(TM) 64-Bit Server VM (build 24.51-b03, mixed mode) 
  # javac -version
  javac 1.7.0_51


Installing Tomcat
=================

.. _geonode_create_user_tomcat:

Create tomcat user
------------------
:: 

  adduser -m -s /bin/bash tomcat
  passwd tomcat


Tomcat
------

Let's download and install `Tomcat` first::

    wget http://it.apache.contactlab.it/tomcat/tomcat-7/v7.0.63/bin/apache-tomcat-7.0.63.tar.gz
    tar xvf /apache-tomcat-7.0.63.tar.gz
    mv apache-tomcat-7.0.63 /opt
    ln -s /opt/apache-tomcat-7.0.63 /opt/tomcat

Then prepare a clean instance called ``base`` to be used as a template 
for all tomcat instances::

    mkdir -p /var/lib/tomcat/base/{bin,conf,logs,temp,webapps,work}\
    cp -r /opt/tomcat/conf/* /var/lib/tomcat/base/conf/*

And fix the permissions on the files::

    chown -R tomcat:tomcat /opt/apache*
    chown -R tomcat:tomcat /var/lib/tomcat


Instance manager script
-----------------------

To manage our Tomcat instances create the file ``/etc/systemd/system/tomcat\@.service``
with the following content::

    [Unit]
    Description=Tomcat %I
    After=network.target

    [Service]
    Type=forking
    User=tomcat
    Group=tomcat

    Environment=CATALINA_PID=/var/run/tomcat/%i.pid
    #Environment=TOMCAT_JAVA_HOME=/usr/java/default
    Environment=CATALINA_HOME=/opt/tomcat
    Environment=CATALINA_BASE=/var/lib/tomcat/%i
    Environment=CATALINA_OPTS=

    ExecStart=/opt/tomcat/bin/startup.sh
    ExecStop=/opt/tomcat/bin/shutdown.sh
    #ExecStop=/bin/kill -15 $MAINPID

    [Install]
    WantedBy=multi-user.target

Then make it executable::

   chmod +x /etc/systemd/system/tomcat\@.service

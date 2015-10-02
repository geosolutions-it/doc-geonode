.. _geonode_install_on_centos:

###########################
Install GeoNode application
###########################

Install required libs
=====================

Make sure all the needed libraries are installed::

   yum install git gdal gdal-python geos python-pip python-imaging  python-virtualenv python-devel gcc-c++ python-psycopg2
   yum install libxml2 libxml2-devel libxml2-python libxslt libxslt-devel libxslt-python

Create geonode user
===================

Create the user::

  adduser -m -s /bin/bash geonode
  passwd geonode

Install sources
===============

Log in as geonode.
Create the ``/home/geonode/prj`` directory::

   cd
   mkdir prj
   cd prj


Install GeoNode
---------------

Install GeoNode sources from official repository::

   git clone https://github.com/GeoNode/geonode.git
   
.. important::
   The new features added to GeoNode for handling time series have not yet been added to the official repository.
   We have to use the following commands in place of the previous ``git clone``:: 
   
     git clone https://github.com/geosolutions-it/geonode.git
     cd geonode
     git checkout mosaics_cread
     
Then, as ``root``, install the required libraries::

   cd ~geonode/geonode
   pip install -e . --log jnp-install.log

Updated GeoNode libraries
`````````````````````````

.. important::
   The new features added to GeoNode for handling time series needs some ancillary libraries 
   which have been updated, but have not yet been merged into the related official repositories.
   We have perform these extra steps to upgrade the needed libraries.
   
   Once this code will be merged into the official repos, these steps will no longer be needed.

As ``root`` user::

   mkdir /root/updates
   
Install the updated *gsconfig* library::

   cd /root/updates   
   git clone https://github.com/afabiani/gsconfig.git
   cd gsconfig 
   python setup.py install
  
Install the updated *gsimporter* library::  

   cd /root/updates   
   git clone https://github.com/geosolutions-it/gsimporter.git
   cd gsimporter
   git checkout imagemosaic_support
   python setup.py install
       
.. _geonode_install_settings:

Edit settings
-------------

As ``geonode`` user create the file ``/home/geonode/project/geonode/geonode/local_settings.py``
and insert :download:`this content <resources/local_settings.py.txt>`.

Then edit the file ``local_settings.py`` and replace passwords and constants:

- ``SITEURL`` 
- ``ALLOWED_HOSTS``
- ``DATABASES / default / PASSWORD``: this is the password for the DB user ``geonode``
- ``DATABASES / datastore / PASSWORD``: this is the password for the DB user ``geonode-import``
- ``OGC_SERVER / PUBLIC_LOCATION``
- ``OGC_SERVER / PASSWORD`` : this is the password we will set into GeoServer (see :ref:`geonode_install_geoserver_pw`)


Install C-READ code
-------------------

As user ``geonode`` download the sources::

   cd
   cd prj
   git clone https://github.com/geosolutions-it/geonode-cread.git cread

Init GeoNode
------------

As user ``geonode``, init the db, by creating the schema tables and populating the static data::

   cd
   cd prj/cread
   python manage.py syncdb --noinput

Create the superuser::

   python manage.py createsuperuser
   
Then move all the static data on its own, so that they will be served by the httpd server.

As ``root`` create the directories used for static data and for the user uploads:: 

   mkdir -vp /var/lib/geonode/{uploaded,static}
   chown geonode: -R /var/lib/geonode

As ``geonode``, from inside ``/home/geonode/prj/cread``, run ::
 
   python manage.py collectstatic

   
      
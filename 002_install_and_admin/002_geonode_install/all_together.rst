.. _all_together:

===================
Finish installation
===================

In previous sections you' ve setup all the applications we need to run GeoNode.

In this section we are going to `glue` all the pieces together and finalize GeoNode
setup.

Initialize the Database
=======================

TODO

python manage.py syncdb --noinput

Create Superuser
================

TODO

python manage.py createsuperuser
   sysadmin/sysgeonode

GeoNode Configuration
=====================

TODO

cd /home/geonode/geonode/geonode/
cp local_settings.py.sample local_settings.py
gedit local_settings.py

.. _geonode_create_db:

#################
Create GeoNode DB
#################

First create the geonode user. GeoNode is going to use this user to access the database:
::

    sudo -u postgres createuser -P geonode

You will be prompted asked to set a password for the user. Enter geonode as password

Create geonode database with owner geonode
::

    createdb -O geonode geonode

And database geonode_data with owner geonode
::
    sudo -u postgres createdb -O geonode geonode_data

Switch to user postgres and create PostGIS extension:
::

    sudo su postgres
    psql -d geonode_data -c 'CREATE EXTENSION postgis;'

Then adjust permissions
::

    psql -d geonode_data -c 'GRANT ALL ON geometry_columns TO PUBLIC;'
    psql -d geonode_data -c 'GRANT ALL ON spatial_ref_sys TO PUBLIC;'

And exit `postgres` user
::
    exit

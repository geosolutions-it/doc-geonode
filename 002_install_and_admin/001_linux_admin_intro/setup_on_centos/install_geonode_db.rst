.. _geonode_create_db:

#################
Create GeoNode DB
#################

As user ``root``, switch to user ``postgres``::

   su - postgres

As user ``postgres`` create the ``geonode`` DB user, and set a password for it::

   createuser -P geonode 

Then create the DB that will be used by GeoNode::
    
   createdb -O geonode geonode

Now we need a *PostGIS* database where GeoServer will store the geographic data.    

A new table for every new vector layer will be created, so the pg user should be able to create tables::

   createuser -P --createdb geonode-imports
   
Then create the DB::

   createdb -O geonode-imports geonode-imports

Now add the GIS extensions.
As user ``postgres``:: 
   
   psql geonode-imports
   
   create extension "postgis";
   create extension "postgis_topology";

   GRANT ALL ON geometry_columns TO PUBLIC;
   GRANT ALL ON spatial_ref_sys TO PUBLIC;
   GRANT ALL ON SCHEMA topology TO PUBLIC;
    


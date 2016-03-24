.. _geonode_data_migration_workshop:

======================================
Migrate Data Between GeoNode Instances
======================================

This workshop shows how to migrate GeoNode Layers (along with GeoServer associated datasets and styles) from an instance to another.

The whole tutorial is divided in different parts, each one showing a different methodology to perform the data migration:

#. Manual migration of data between to GeoNode instances with same version

#. Semi-automatic migration of data between GeoNode instances with same version

#. Automatic migration of data between GeoNode instances with same version

#. Automatic migration of data between GeoNode instances with different version

.. warning:: Currently the points 2,3,4 are not yet feasable with the actual GeoNode version. Those section will be available as soon as the "**GeoNode Backup & Restore**" `GNIP <https://github.com/GeoNode/geonode/issues/2401>`_ development will be ready and merged to the ``master`` branch.

Manual migration of data between GeoNode instances with same version
====================================================================

This section shows how to export a Layer from a GeoNode instance and import it back to another one with the same versions.

Before going through the commands and the operations to perform the export/import tasks, the tutorial will explain in details the structure of a GeoNode Layer.

As you may already know, the physical geospatial data, along with its graphical stylesheets (also known as **SLDs**), are backed by GeoServer.
Each GeoNode version is shipped with a related GeoServer version and GeoServer Data Dir (we will see in details later what does it means).
For the moment the important thing to know is that GeoNode cannot live without a running instance of GeoServer. Therefore migrating data from a GeoNode instance to another one,
means also move geospatial data and stylesheets between the related GeoServer instances.

A GeoNode Layer Structure
-------------------------
Lets start anlysing deeply a GeoNode Layer structure.

Preparation Of The Webinar
^^^^^^^^^^^^^^^^^^^^^^^^^^
This tutorial is generic and can be executed using any existing Layer of GeoNode, however in order to follow exactly the same passages, please execute this first simple exercise.

.. warning:: As a prerequisite, you must have access to a GeoNode instance with Admin rights.

**Exercise**

*Add A Sample Layer To GeoNode*

1. Log into GeoNode as ``Administrator``

2. Click on the ``Add Layers`` button from the home page, in order to switch to the upload page

   .. figure:: img/layer_add_layers_button.jpg

      *GeoNode Add Layers Button*

3. Click on the ``Browse`` on the upload page

   .. figure:: img/layer_upload_browse_button.jpg

      *GeoNode Upload Browse Button*

4. Select from the folder ``gisdata/data/good/vector`` the **4** files

    - san_andres_y_providencia_coastline.dbf

    - san_andres_y_providencia_coastline.prj

    - san_andres_y_providencia_coastline.shp

    - san_andres_y_providencia_coastline.shx

    
   .. figure:: img/layer_upload_coastline.jpg

      *GeoNode Upload Browse Button*

5. Click on the ``Upload Files`` button and make sure the operation completes successfully
  
   .. figure:: img/layer_upload_upload_files.jpg

      *GeoNode Upload Files Button*


Layer Metadata
^^^^^^^^^^^^^^
Each resource in GeoNode has metadata. Metadada in GeoNode is quite important, it is used by the application to describe, search and identify a resource. 
As an instance part the title, abstract, regions or keywords of a resource are all part of the metadata.

Metadata in GeoNode is stored on the backend databse as a set of fields associated to the resource. The catalogue service then, makes use of such information to dynamically generate ISO compliant XML records.
Those records can be used by external catalogiung applications, compliant with the standards supported by GeoNode, in order to automatically recognize and indexing the available resources on the server.

The GeoNode Layer info page makes use of some metadata fields to provide immediate description of the Layer.

As shown in the figure below, the GeoNode ``Info`` tab contains a table reporting basic information about the Layer, like the *Title*, the *Abstract*, the *Category* and others.

   .. figure:: img/layer_metadata_001.jpg

      *GeoNode Layer Info*

.. warning:: The *Title* shown on the Layer Metadata **is not** the real Layer name. We will deepen the topic on the following sections of this tutorial.

In order to obtain the whole Layer Metadata in one standard format (usually not quite human-friendly; a huge and long XML), it is possible to click on the ``Download Metadada`` button on the right panel.
GeoNode will present to the user a modal window with a list of permalinks to the dynamic XML supported formats.

   .. figure:: img/layer_metadata_002.jpg

      *GeoNode Layer Download Metadada*

As an instance, if you click on ``ISO``, you will get an XML containing all the Layer metadata fields in ``ISOTC211/19115`` format.

   .. figure:: img/layer_metadata_003.jpg

      *GeoNode Layer Download Metadada ISOTC211/19115 Format*

**Excercise**

*Edit The Layer Metadada*

1. Go to the ``GeoNode Layer List``

   .. figure:: img/layer_metadata_004.jpg

      *GeoNode Layer List*

2. Click on a ``Layer`` in order to go to the resource info page

   .. figure:: img/layer_metadata_005.jpg

      *GeoNode Layer Edit*

3. Click on the ``Edit Layer`` button

   .. figure:: img/layer_metadata_006.jpg

      *GeoNode Layer Download Metadada Button*

4. Click on the ``Edit`` button under the ``Metadada`` incon of the modal window

   .. figure:: img/layer_styles_002.jpg

      *GeoNode Metadada Edit*

5. Update the at least the ``Title``, the ``Abstract`` and the ``Category`` and finally click on the ``Update`` button

   .. figure:: img/layer_edit_metadata_001.jpg

      *GeoNode Metadada Edit*



**The export** of metadata is a foundamental task to achieve when moving a resource from a GeoNode instance to another.

**Excercise**

*Export the Layer Metadata as ISOTC211/19115 and save it to an XML files on the local storage*

1. Go to the ``GeoNode Layer List``

   .. figure:: img/layer_metadata_004.jpg

      *GeoNode Layer List*

2. Click on a ``Layer`` in order to go to the resource info page

   .. figure:: img/layer_metadata_005.jpg

      *GeoNode Layer Edit*

3. Click on the ``Download Metadada`` button

   .. figure:: img/layer_metadata_006.jpg

      *GeoNode Layer Download Metadada Button*

4. From the modal window, click with the **right mouse button** over the ``ISO`` link

   .. figure:: img/layer_metadata_007.jpg

      *GeoNode Layer Download Metadada ISOTC211/19115 Format*

5. From the context menù, select the voice **Save Link As**

   .. figure:: img/layer_metadata_008.jpg

      *GeoNode Layer Download Metadada ISOTC211/19115 Save Link As*

6. Store the xml into the hard disk and note the location for later use

   .. figure:: img/layer_metadata_009.jpg

      *GeoNode Layer Download Metadada ISOTC211/19115 XML*


Layer Styles
^^^^^^^^^^^^
Each Layer in GeoNode has a representation style associated, or a ``Legend`` if you want.

A style is basically a set of rules instructing the geospatial server on how to create a portrayal of the original data. The figure shown in the map is only one of the inifite possible representation of the data stored on the server.

It is worth to point out that **viewing the data** is substantially different from **getting the data**. A portrayal of data provides to the users an immediate understanding of the meaning (or at least of one possible meaning), but this is not suitable for data analysis or more sophisticated computational tasks.

The legend (or style), depends exclusvely from the geometry of the layer and, optionally, from a subset of its atributes.

On GeoNode, if you move to a Layer info page, you can notice a small ``Legend`` panel on the right representing the style currently in use.

   .. figure:: img/layer_styles_001.jpg

      *GeoNode Layer Download Metadada ISOTC211/19115 XML*


A Layer can have a lot of different styles associated, of course. Usually there is a ``Default Style`` which is the one presented to the users if not differently specified.

It is possible from the GeoNode interface to manage the styles associated to a Layer and also change its ``Default Style``.

.. note:: Only owners or users with write permissions on the Layer can update the styles.

**Excercise**

*Layer Styles Management Panel*

1. Go to the ``GeoNode Layer List``

   .. figure:: img/layer_metadata_004.jpg

      *GeoNode Layer List*

2. Click on a ``Layer`` in order to go to the resource info page

   .. figure:: img/layer_metadata_005.jpg

      *GeoNode Layer Edit*
      
3. Click on the ``Edit Layer`` button on the right panel

   .. figure:: img/layer_edit_button.jpg

      *GeoNode Layer Edit Button*

4. Click on the ``Manage`` button under the ``Styles`` incon of the modal window

   .. figure:: img/layer_styles_002.jpg

      *GeoNode Layer Styles Manage*

5. Play with the styles comboboxex in order to change the ``Default Style`` or add/remove more of them **without** updating the Layer

   .. figure:: img/layer_styles_003.jpg

      *GeoNode Layer Styles Management Panel*

.. warning:: **Do not** click on ``Update Available Styles`` button, otherwise you will change the current Layer styles.


**GeoNode also** provides a simple tool for the editing of the Layer style directly from the web interface.

.. note:: It is worth noting that the GeoNode style editor is very simple and does not allow advanced style editing. Also this tool may not work perfectly with complex layers. Further in the tutorial we will see how it is possible to edit directly the style using the ``SLD`` native format.

**Excercise**

*Update the default style through the GeoNode Style Editor tool*

1. Go to the ``GeoNode Layer List``

   .. figure:: img/layer_metadata_004.jpg

      *GeoNode Layer List*

2. Click on a ``Layer`` in order to go to the resource info page

   .. figure:: img/layer_metadata_005.jpg

      *GeoNode Layer Edit*
      
3. Click on the ``Edit Layer`` button on the right panel

   .. figure:: img/layer_edit_button.jpg

      *GeoNode Layer Edit Button*

4. Click on the ``Edit`` button under the ``Styles`` incon of the modal window

   .. figure:: img/layer_styles_002.jpg

      *GeoNode Layer Styles Edit*

5. You should see a small window similar to the one depicted below

   .. figure:: img/layer_styles_004.jpg

      *GeoNode Layer Styles Editor*


6. Select the first ``Rule`` and click on the small ``Edit`` button below

   .. figure:: img/layer_styles_006.jpg

   .. figure:: img/layer_styles_005.jpg
   
      *GeoNode Layer Styles Edit Rules*

7. Modify the ``Name``, the ``Color`` and the ``Width`` of the stroke and click save

   .. figure:: img/layer_styles_007.jpg

      *GeoNode Layer Styles Edit Stroke*


**The GeoNode style** editor tool just simplifies the editing of a Layer style by providing a small graphic user interface to one of the GeoServer capabilities.

Under the hood a Layer style is a special XML format defined from the Open Geospatial Consortium (OGC) as ``Style Layer Descriptor`` or **SLD**.

Advanced users can directly modify the SLD or use more advanced tools to create very complex and beautiful Layer styles.

In order to do that, you will need to update the SLD source directly through the GeoServer interface.

**Excercise**

*Update the default style through the GeoServer interface*

1. Log inot GeoNode as ``Administrator``. Then click on the user button on the top right.

   .. figure:: img/layer_styles_008.jpg

      *GeoNode Admin*

2. From the menu, click on the ``GeoServer`` voice.

   .. figure:: img/layer_styles_009.jpg

      *GeoNode Admin GeoServer*

3. You will be redirected to the ``GeoServer`` admin interface.

   .. figure:: img/layer_styles_010.jpg

      *GeoServer Admin Gui*

4. Select the ``Styles`` topic from the left menu.

   .. figure:: img/layer_styles_011.jpg

      *GeoServer Admin Styles*

5. Select the layer name from the list and click on it. You will be redirected to the SLD editor page.

   .. figure:: img/layer_styles_012.jpg

      *GeoServer Admin Style Editor*

6. Modify the ``Color`` and the ``Width`` of the ``External Border`` XML rule. Click on ``Preview legend`` to see the changes, and when you are happy ``Submit`` the SLD.

   .. figure:: img/layer_styles_013.jpg

      *GeoServer Admin Style Editor*

7. Go back to GeoNode. Reload the Layer in order to see the changes.

   .. figure:: img/layer_styles_014.jpg

      *GeoNode Updated Layer Style*
      
GeoServer Data Dir Structure
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
This section gives an overview of the structure and contents of the GeoServer data directory. 


This is not intended to be a complete reference to the GeoServer configuration information, 
since generally the data directory configuration files should not be accessed directly.
Instead, the `Web Administration Interface <http://docs.geoserver.org/stable/en/user/webadmin/index.html#web-admin>`_ can be used to view and modify the configuration manually, 
and for programmatic access and manipulation 
the `REST configuration <http://docs.geoserver.org/stable/en/user/rest/index.html#rest>`_ API should be used.

The directories that do contain user-modifiable content are:
``logs``, ``palettes``, ``templates``, ``user-projection``, and ``www``.

The following figure shows the structure of the GeoServer data directory:

   .. code-block:: console
      :linenos:
      
       <data_directory>/
       
          global.xml
          logging.xml
          wms.xml
          wfs.xml
          wcs.xml
          
          data/
          demo/
          geosearch/
          gwc/
          layergroups/
          logs/
          palettes/
          plugIns/
          security/
          styles/
          templates/
          user_projections/
          workspaces/
            |
            +- workspace dirs...
               |
               +- datastore dirs...
                  |
                  +- layer dirs...
          www/

**The .xml files**

The top-level ``.xml`` files contain information about the services and various global options for the server instance. 

.. list-table::
   :widths: 20 80

   * - **File**
     - **Description**
   * - ``global.xml``
     - Contains settings common to all services, such as contact information, JAI settings, character sets and verbosity.
   * - ``logging.xml``
     - Specifies logging parameters, such as logging level, logfile location, and whether to log to stdout.  
   * - ``wcs.xml`` 
     - Contains the service metadata and various settings for the WCS service.
   * - ``wfs.xml`` 
     - Contains the service metadata and various settings for the WFS service.
   * - ``wms.xml`` 
     - Contains the service metadata and various settings for the WMS service.


**workspaces**

The ``workspaces`` directory contain metadata about the layers published by GeoServer.
It contains a directory for each defined **workspace**.
Each workspace directory contains directories for the **datastores** defined in it.
Each datastore directory contains directories for the **layers** defined for the datastore.
Each layer directory contains a ``layer.xml`` file, and 
either a ``coverage.xml`` or a ``featuretype.xml`` file 
depending on whether the layer represents a *raster* or *vector* dataset.

**data**

The ``data`` directory can be used to store file-based geospatial datasets being served as layers.
(This should not be confused with the main "GeoServer data directory".)
This directory is commonly used to store shapefiles and raster files, 
but can be used for any data that is file-based.

The main benefit of storing data files under the ``data`` directory is portability. 
Consider a shapefile stored external to the data directory at a location ``C:\gis_data\foo.shp``. 
The ``datastore`` entry in ``catalog.xml`` for this shapefile would look like the following:

   .. code-block:: xml
      :linenos:
      
       <datastore id="foo_shapefile">
          <connectionParams>
            <parameter name="url" value="file://C:/gis_data/foo.shp" />
          </connectionParams>
        </datastore>

Now consider trying to port this data directory to another host running GeoServer. 
The location ``C:\gis_data\foo.shp`` probably does not exist on the second host. 
So either the file must be copied to this location on the new host, 
or ``catalog.xml`` must be changed to reflect a new location.

This problem can be avoided by storing ``foo.shp`` in the ``data`` directory. 
In this case the ``datastore`` entry in ``catalog.xml`` becomes:

   .. code-block:: xml
      :linenos:
      
        <datastore id="foo_shapefile">
          <connectionParams>
            <parameter name="url" value="file:data/foo.shp"/>
          </connectionParams>
        </datastore>

The ``value`` attribute is rewritten to be relative to the ``data`` directory. 
This location independence allows the entire data directory to be copied to a new host 
and used directly with no additional changes.

**demo**

The ``demo`` directory contains files which define the *sample requests* available in the *Sample Request Tool* (http://localhost/geoserver/demoRequest.do). 
See the `Demos <http://docs.geoserver.org/stable/en/user/webadmin/demos/index.html#webadmin-demos>`_ page for more information.

**geosearch**

The ``geosearch`` directory contains information for regionation of KML files.

**gwc**

The ``gwc`` directory holds the cache created by the embedded GeoWebCache service.

**layergroups**

The ``layergroups`` directory contains configuration information for the defined layergroups.

**logs**

The ``logs`` directory contains configuration information for logging profiles, 
and the default ``geoserver.log`` log file.
See also `Advanced log configuration <http://docs.geoserver.org/stable/en/user/advanced/logging.html#logging>`_.

**palettes**

The ``palettes`` directory is used to store pre-computed **Image Palettes**. 
Image palettes are used by the GeoServer WMS as way to reduce the size of produced images while maintaining image quality.
See also `Paletted Images <http://docs.geoserver.org/stable/en/user/tutorials/palettedimage/palettedimage.html#tutorials-palettedimages>`_.

**security**

The ``security`` directory contains the files used to configure the GeoServer security subsystem. This includes a set of property files which define *access roles*, along with the services and data each role is authorized to access. See the `Security <http://docs.geoserver.org/stable/en/user/security/index.html#security>`_ section for more information.

**styles**

The ``styles`` directory contains Styled Layer Descriptor (SLD) files which contain styling information used by the GeoServer WMS. For each file in this directory there is a corresponding entry in ``catalog.xml``:

   .. code-block:: xml
      :linenos:
      
        <style id="point_style" file="default_point.sld"/>

See the `Styling <http://docs.geoserver.org/stable/en/user/styling/index.html#styling>`_ section for more information about styling and SLD .

**templates**

The ``templates`` directory contains files used by the GeoServer **templating** subsystem. 
Templates are used to customize the output of various GeoServer operations.
See also `Freemarker Templates <http://docs.geoserver.org/stable/en/user/tutorials/freemarker.html#tutorial-freemarkertemplate>`_.

**user_projections**

The ``user_projections`` directory contains a file called ``epsg.properties`` which is used to define custom spatial reference systems that are not part of the official `EPSG database <http://www.epsg.org/CurrentDB.html>`_.
See also `Custom CRS Definitions <http://docs.geoserver.org/stable/en/user/advanced/crshandling/customcrs.html#crs-custom>`_.

**www**

The ``www`` directory is used to allow GeoServer to serve files like a regular web server. 
The contents of this directory are served at ``http:/<host:port>/geoserver/www``.
While not a replacement for a full blown web server, 
this can be useful for serving client-side mapping applications.
See also `Serving Static Files <http://docs.geoserver.org/stable/en/user/tutorials/staticfiles.html#tutorials-staticfiles>`_.

**Excercise**

*Navigate the GeoServer Data Directory*

1. Log inot GeoNode as ``Administrator``. Then click on the user button on the top right.

   .. figure:: img/layer_styles_008.jpg

      *GeoNode Admin*

2. From the menu, click on the ``GeoServer`` voice.

   .. figure:: img/layer_styles_009.jpg

      *GeoNode Admin GeoServer*

3. You will be redirected to the ``GeoServer`` admin interface.

   .. figure:: img/layer_styles_010.jpg

      *GeoServer Admin Gui*

4. Select the ``Server Status`` topic from the left menu. On the status page note the ``Data Directory``.

   .. figure:: img/layer_gs_datadir_001.jpg

      *GeoServer Admin Server Status Page*

5. Open a ``Terminal`` window and go to the GeoServer Data Directory folder. Navigate the folders and examine the files.

6. Enter the ``styles`` directory and confirm it is present the file ``san_andres_y_providencia_coastline.sld``

   .. code-block:: console
      :linenos:

      $ cat san_andres_y_providencia_coastline.sld


GeoServer Workspaces
^^^^^^^^^^^^^^^^^^^^
TODO

GeoServer Layers
^^^^^^^^^^^^^^^^
TODO

GeoServer Stylesheets (SLD)
^^^^^^^^^^^^^^^^^^^^^^^^^^^
TODO

Steps To Manually Migrate A Layer
---------------------------------
TODO

1. Download a Layer as ESRI Shapefile
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
TODO

2. Save and exrpot the Layer SLDs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
TODO

3. Import back the Layer through the "importlayers" GeoNode Management Command
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
TODO

Final Checks And Hints
----------------------
TODO


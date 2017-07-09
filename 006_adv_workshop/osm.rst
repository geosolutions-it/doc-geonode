.. _osm2pgsql:

Loading OSM Data into GeoNode
=============================

In this section, we will walk through the steps necessary to load OSM data into your GeoNode project. As discussed in previous sections, your GeoNode already uses OSM tiles from MapQuest and the main OSM servers as some of the available base layers. This session is specifically about extracting actual data from OSM and converting it for use in your project and potentially for Geoprocessing tasks.

The first step in this process is to get the data from OSM. We will be using the OSM Overpass API since it lets us do more complex queries than the OSM API itself. You should refer to the OSM Overpass API documentation to learn about all of its features. It is an extremely powerful API that lets you extract data from OSM using a very sophisticated API. 

- http://wiki.openstreetmap.org/wiki/Overpass_API
- http://wiki.openstreetmap.org/wiki/Overpass_API/Language_Guide

In this example, we will see a couple of examples extracting:

    - Southwest Platte River Road footprint, which runs through Denver
    
    - Building footprint data around Kampala, Uganda.

To do this we will use an interactive tool that makes it easy construct a Query against the Overpass API.

Exporting OSM data to shapefile using QGIS
------------------------------------------

#. First go to `openstreetmap.org <http://openstreetmap.org>`_, and search for "South Platte River, Denver"

    .. image:: img/osm_river1.png
       :width: 400px
       :alt: Search on OpenStreetMap
    *Search on OpenStreetMap*

#. Zoom in, until you see the features appearing

    .. image:: img/osm_river2.png
       :width: 400px
       :alt: Features on OpenStreetMap
    *Features on OpenStreetMap*

#. Select a feature. In this example we selected *Way: Southwest Platte River Road (17082695)*

    .. image:: img/osm_river3.png
       :width: 400px
       :alt: Southwest Platte River Road
    *Southwest Platte River Road*

#. Identify the tags and values of the features you're after by

    - Zooming all the way into the map
    - Click on the layers icon on the right (the three sheets of paper)
    - Click on the last menu entry (``Map data`` or something similar in your language)
    - The features on the map turn blue (make sure you're zoomed in far enough to see
    - Click on the feature you're after
    - The Tags and Values appear on left side of the screen, and you can proceed below...
    
    .. image:: img/osm_river4.png
       :width: 400px
       :alt: Southwest Platte River Road - Details
    *Southwest Platte River Road - Details*
    
#. Point your browser at `overpass-turbo.eu <http://overpass-turbo.eu/>`_, and use the search box to zoom to the area of interest, in this case *Colorado*

    .. image:: img/osm_river5.png
       :width: 400px
       :alt: Colorado
    *Colorado*

#. Click on the ``Wizard`` button, and enter the search text accordingly to the information retrieved from OpenStreetMap

    .. image:: img/osm_river6.png
       :width: 400px
       :alt: Southwest Platte River Road
    *Southwest Platte River Road*

    .. code::
    
        name="Southwest Platte River Road" and highway=tertiary and railway=abandoned

#. Click on the button ``Build and Run Query``, the map shows the selected data

    .. image:: img/osm_river7.png
       :width: 400px
       :alt: Southwest Platte River Road Data
    *Southwest Platte River Road Data*

#. Click on the button ``Export``, and download data as ``GeoJSON``

    .. image:: img/osm_river8.png
       :width: 400px
       :alt: Southwest Platte River Road Export
    *Southwest Platte River Road Export*

#. Save it and confirm. The file ``export.geojson`` will be created into the ``Downloads`` folder

    .. image:: img/osm_river9.png
       :width: 400px
       :alt: export.geojson
    *export.geojson*

#. Rename the file  to ``south_platte_river_road.geojson``

    .. image:: img/osm_river10.png
       :width: 400px
       :alt: south_platte_river_road.geojson
    *south_platte_river_road.geojson*

#. Open *QGis Client* and import the layer into it

    .. image:: img/osm_river11.png
       :width: 400px
       :alt: QGis Client
    *QGis Client*

#. Click with the right button over the layer and then click on ``Save As``

    .. image:: img/osm_river12.png
       :width: 400px
       :alt: QGis Client - Save As
    *QGis Client - Save As*

#. Select ``ESRI Shapefile`` and click on ``Browse``

    .. image:: img/osm_river13.png
       :width: 400px
       :alt: QGis Client - Save As SHP
    *QGis Client - Save As SHP*

#. Select the ``Downloads`` folder and name the file ``south_platte_river_road``

    .. image:: img/osm_river14.png
       :width: 400px
       :alt: QGis Client - Save As SHP
    *QGis Client - Save As SHP*

#. This will save the layer as a Shapefile, which can be easily imported into GeoNode

    .. image:: img/osm_river15.png
       :width: 400px
       :alt: South Platte River Road into GeoNode
    *South Platte River Road into GeoNode*

Let's see another example of export through the OverPass APIs.

#. Point your browser at `overpass-turbo.eu <http://overpass-turbo.eu/>`_, and use the search box to zoom to the area of interest, in this case *Kampala*

    .. image:: img/osm_kampala1.png
       :width: 400px
       :alt: Kampala
    *Kampala*

#. Zoom around *Nakasero* area

    .. image:: img/osm_kampala2.png
       :width: 400px
       :alt: Nakasero
    *Nakasero*

#. Select the desired *Bounding Box* around the area to export

    .. image:: img/osm_kampala3.png
       :width: 400px
       :alt: Nakasero BBOX

    .. image:: img/osm_kampala4.png
       :width: 400px
       :alt: Nakasero BBOX
    *Nakasero BBOX*
   
#. Run the ``Wizard`` and write ``building=* or highway=* in bbox`` on the text box.

    .. image:: img/osm_kampala5.png
       :width: 400px
       :alt: Nakasero Query Builder
    *Nakasero Query Builder*

    This will result in a query like the following one
    
    .. code:: java

        /*
        This has been generated by the overpass-turbo wizard.
        The original search was:
        “building=* or highway=* in bbox”
        */
        [out:json][timeout:25];
        // gather results
        (
          // query part for: “building=*”
          node["building"]({{bbox}});
          way["building"]({{bbox}});
          relation["building"]({{bbox}});
          // query part for: “highway=*”
          node["highway"]({{bbox}});
          way["highway"]({{bbox}});
          relation["highway"]({{bbox}});
        );
        // print results
        out body;
        >;
        out skel qt;

#. Export data as ``GeoJSON`` like before, rename it and use QGis to export as a Shapefile

    .. image:: img/osm_kampala6.png
       :width: 400px
       :alt: Nakasero Query Builder
    *Nakasero Query Builder*

#. This will allow you to save all the layers as a Shapefiles, which can be easily imported into GeoNode

    .. image:: img/osm_kampala7.png
       :width: 400px
       :alt: Nakasero into GeoNode
    *Nakasero into GeoNode*

.. note:: You can also rename the file in your Operating Systems File management tool (Windows Explorer, Finder etc).

.. note:: You may want to switch to an imagery layer in order to more easily see the buildings on the OSM background.

Exporting OSM data to shapefile using GDAL
------------------------------------------

An alternative way to export the ``.osm`` or ``.geojson`` file to a shapefile is to use `ogr2ogr <http://www.gdal.org/ogr2ogr.html>`_ combined with the `GDAL osm driver <http://www.gdal.org/ogr/drv_osm.html>`_, available from GDAL version 1.10.


As a first step, inspect how the GDAL osm driver sees the .osm file using the ogrinfo command::

    $ ogrinfo -al -so nakasero.geojson -where "OGR_GEOMETRY='Point'"
    
    INFO: Open of `nakasero.geojson'
          using driver `GeoJSON' successful.

    Layer name: OGRGeoJSON
    Geometry: Unknown (any)
    Feature Count: 142
    Extent: (32.573864, 0.312602) - (32.593496, 0.331627)
    Layer SRS WKT:
    GEOGCS["WGS 84",
        DATUM["WGS_1984",
            SPHEROID["WGS 84",6378137,298.257223563,
                AUTHORITY["EPSG","7030"]],
            AUTHORITY["EPSG","6326"]],
        PRIMEM["Greenwich",0,
            AUTHORITY["EPSG","8901"]],
        UNIT["degree",0.0174532925199433,
            AUTHORITY["EPSG","9122"]],
        AUTHORITY["EPSG","4326"]]
    id: String (0.0)
    ...

    $ ogrinfo -al -so nakasero.geojson -where "OGR_GEOMETRY='LineString'"

    INFO: Open of `nakasero.geojson'
          using driver `GeoJSON' successful.

    Layer name: OGRGeoJSON
    Geometry: Unknown (any)
    Feature Count: 928
    Extent: (32.571923, 0.306984) - (32.597590, 0.338549)
    Layer SRS WKT:
    GEOGCS["WGS 84",
        DATUM["WGS_1984",
            SPHEROID["WGS 84",6378137,298.257223563,
                AUTHORITY["EPSG","7030"]],
            AUTHORITY["EPSG","6326"]],
        PRIMEM["Greenwich",0,
            AUTHORITY["EPSG","8901"]],
        UNIT["degree",0.0174532925199433,
            AUTHORITY["EPSG","9122"]],
        AUTHORITY["EPSG","4326"]]
    id: String (0.0)
    ...
    
    $ ogrinfo -al -so nakasero.geojson -where "OGR_GEOMETRY='Polygon'"

    INFO: Open of `nakasero.geojson'
          using driver `GeoJSON' successful.

    Layer name: OGRGeoJSON
    Geometry: Unknown (any)
    Feature Count: 2596
    Extent: (32.572918, 0.311164) - (32.594049, 0.333597)
    Layer SRS WKT:
    GEOGCS["WGS 84",
        DATUM["WGS_1984",
            SPHEROID["WGS 84",6378137,298.257223563,
                AUTHORITY["EPSG","7030"]],
            AUTHORITY["EPSG","6326"]],
        PRIMEM["Greenwich",0,
            AUTHORITY["EPSG","8901"]],
        UNIT["degree",0.0174532925199433,
            AUTHORITY["EPSG","9122"]],
        AUTHORITY["EPSG","4326"]]
    id: String (0.0)
    ...

    $ ogrinfo -al -so nakasero.geojson -where "OGR_GEOMETRY='MultiPolygon'"
    
    INFO: Open of `nakasero.geojson'
          using driver `GeoJSON' successful.

    Layer name: OGRGeoJSON
    Geometry: Unknown (any)
    Feature Count: 3
    Extent: (32.576421, 0.315224) - (32.590876, 0.330137)
    Layer SRS WKT:
    GEOGCS["WGS 84",
        DATUM["WGS_1984",
            SPHEROID["WGS 84",6378137,298.257223563,
                AUTHORITY["EPSG","7030"]],
            AUTHORITY["EPSG","6326"]],
        PRIMEM["Greenwich",0,
            AUTHORITY["EPSG","8901"]],
        UNIT["degree",0.0174532925199433,
            AUTHORITY["EPSG","9122"]],
        AUTHORITY["EPSG","4326"]]
    id: String (0.0)
    ...

ogrinfo has detected 4 different geometric layers inside the osm data source. As we are just interested in the buildings, you will just export to a new shapefile the polygons and multipolygons layer using the GDAL ogr2ogr command utility::

    $ ogr2ogr nakasero_buildings nakasero.geojson -where "OGR_GEOMETRY='Polygon' or OGR_GEOMETRY='MultiPolygon'" -nln nakasero_buildings

Now you can upload the shapefile to GeoNode using the GeoNode Upload form in the same manner as you did in the previous section.

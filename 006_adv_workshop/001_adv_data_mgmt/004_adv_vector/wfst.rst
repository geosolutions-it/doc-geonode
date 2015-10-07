.. _geoserver.vector_data.wfst:

Modifying Feature Types
-----------------------

GeoServer provides a fully Transactional Web Feature Service (WFS-T) which enables users to insert/delete/modify the avilable FeatureTypes.
This section shows a few of the GeoServer WFS-T capabilities and interactions with desktop GIS clients.

#. Open `uDig <http://udig.refractions.net>`_ GIS desktop client by going on the command line, changing directory in the training root if necessary, and running the ``udig`` commmand.

#. Add GeoServer WFS to the catalog.

   .. figure:: img/wfs-t1.png

   Use the import button in the catalog tab, and select "data" in the first page of the wizard

   .. figure:: img/wfs-t2.png
      
      Selection of Web Feature Service data

   Insert into the URL text box the following address::
   
     http://localhost:8083/geoserver/wfs?request=GetCapabilities&service=WFS

   .. figure:: img/wfs-t3.png
      
      The WFS URL
      
   Select the `Mainrd` from the list
   
   .. figure:: img/wfs-t4.png
   
      WFS Datasets shown into the uDig catalog

#. Load the `Mainrd` Feature Type using *drag-n-drop*.

   .. figure:: img/wfs-t5.png
      
      Importing `Mainrd` into the map

#. Perform a zoom operation on the upper-right part of the layer.

   .. figure:: img/wfs-t6.png

      Zooming in ...
   
   .. figure:: img/wfs-t7.png

      Zooming in ...

#. By using the :guilabel:`Select and Edit Geometry` tool try to move/add/remove some vertex to the small line at the center of the screen.

   .. figure:: img/wfs-t8.png
   
      Playing with the Geometry

#. Once finished use the :guilabel:`Commit` tool to persist the changes on GeoServer.

   .. figure:: img/wfs-t9.png

      Committing changes throught the WFS-T protocol

#. Use GeoServer **Layer Preview** to view the changes on the `Mainrd` layer.
   
   .. warning:: In order to view the streets lines you have to specify the `line` style on the GetMap request.
   
   .. figure:: img/wfs-t10.png

      Showing the changes to the `Mainrd` Feature Type

#. On uDig look the Feature attribute values using the :guilabel:`Info` tool.

   .. figure:: img/wfs-t11.png
	  
      Retrieving Feature Type info from uDig interface

#. Now open/create the ``request.xml`` file in the training root dir and set in the following requst, which will be used to issue an **Update** Feature type request to the WFS-T updating all roads labelled as ``Monarch Rd`` to ``Monarch Road``

   .. code-block:: xml

      <wfs:Transaction xmlns:topp="http://www.openplans.org/topp" xmlns:ogc="http://www.opengis.net/ogc" xmlns:wfs="http://www.opengis.net/wfs" service="WFS" version="1.0.0">
        <wfs:Update typeName="geosolutions:Mainrd">
              <wfs:Property>
                <wfs:Name>LABEL_NAME</wfs:Name>
                <wfs:Value>Monarch Road</wfs:Value>
              </wfs:Property>
              <ogc:Filter>
                <ogc:PropertyIsEqualTo>
                    <ogc:PropertyName>LABEL_NAME</ogc:PropertyName>
                    <ogc:Literal>Monarch Rd</ogc:Literal>
                </ogc:PropertyIsEqualTo>
              </ogc:Filter>
        </wfs:Update>
      </wfs:Transaction>

#. Issue the WFS-T request towards GeoServer using curl on the command line::

      curl -XPOST -d @request.xml -H "Content-type: application/xml" "http://localhost:8083/geoserver/ows"

#. The response should be a TransactionResponse XML document containing a ``wfs:SUCCESS`` element

#. Ask the info again using the uDig :guilabel:`Info` tool ...

   .. note:: In order to issue a GetFeatureInfo request from the OpenLayers MapPreview tool, just left-click over the line.

   .. figure:: img/wfs-t13.png

      Obtaining the updated Feature Type info from uDig interface

#. Finally, obtain the Feature type info using the GetFeatureInfo operation issued directly by the `Map Preview <http://localhost:8083/geoserver/mapPreview.do>`_ .

   .. figure:: img/wfs-t14.png

      Obtaining the updated Feature Type info from OpenLayers MapPreview GetFeatureInfo


.. _geonode_quickstart:

==================
GeoNode Quickstart
==================
Open Source Geospatial Content Management System

GeoNode is a web-based application and platform for developing geospatial information systems (GIS) and for deploying spatial data infrastructures (SDI).

In this Quickstart guide you will learn the following:

    #. to register a new account to get started
    #. add a new layer
    #. create a map using your new layer
    #. share your map with others

Start GeoNode on your Live DVD or local VM and redirect your browser at `http://localhost/ <http://localhost/>`_ (this is the default port).
The page will look like shown in the image below.

    .. warning:: If GeoNode isn't available by default, you will need to start ``Apache2 HTTPD`` and ``Tomcat8`` Web Servers
    
        .. code::
        
                $> sudo service apache2 restart
                $> sudo service tomcat8 restart

        .. image:: img/services_start.png
           :width: 600px
           :alt: Services Start
        *Services Start*

    .. image:: img/welcome.png
       :width: 600px
       :alt: Welcome page
    *Welcome page*

1. Register a new account
=========================

From the interface shown above, one can view and modify existing spatial layers and maps, as well as find information on other GeoNode users.
But, without being logged in, you are limited to read-only access of public layers.
In order to create a map and add layers to it, you have to have create an account first.

    #. From any page in the web interface, you will see a *Sign in* link. Click that link, and in the dialog that displays, click the *Register now* link.

        .. image:: img/signin_quickstart.png
           :width: 400px
           :alt: Sign in Form
        *Sign in Form*

    #. On the next page, fill out the form. Enter a user name and password in the fields. Also, enter your email address for verification.

        .. image:: img/signup1.png
           :width: 500px
           :alt: Register Form
        *Register Form*

    #. By clicking *Sign up* you will be returned to the homepage. Now you’ve registered an account, you are able to add layers to it as well as create maps and share those with other users.

    .. note:: In case e-mail confirmation has been enabled by configuration (see Advnaced Tutorial and GeoNode documentation for that), you will be returned to the welcome page. An email will be sent confirming that you have signed up. While you are now logged in, you will need to confirm your account. Navigate to the link that was sent in the email.


2. Add a new layer
==================

Layers are a published resource representing a raster or vector spatial data source. Layers also can be associated with metadata, ratings, and comments.

    #. To add a layer to your account, navigate to the welcome page. There the following toolbar can be seen:

        .. image:: img/toolbar_quickstart.png
           :width: 500px
           :alt: Toolbar
        *Toolbar*

    #. By clicking the *Layers* link you will be brought to the *Layers* menu where a new subtoolbar can be seen. This toolbar allows you to *Explore*, *Search* and *Upload* layers.

        .. image:: img/layerstoolbar_quickstart.png
           :width: 500px
           :alt: Upload Button
        *Upload Button*

    #. Now click *Upload Layers* and you’ll see the upload form.

        .. image:: img/uploadform_new_quickstart.png
           :width: 500px
           :alt: Upload Form
        *Upload Form*

    #. You have two possibilities to add your files. You can either do that by using *drag & drop* or you choose to *browse* them. Be aware that you have to upload a complete set of files, consisting of a **shp**, a **prj**, a **dbf** and a **shx** file. If one of them is missing, GeoNode will warn you before you upload them.

    #. You shold now be able to see all the files you want to upload.

        .. image:: img/files_to_be_uploaded.png
           :width: 500px
           :alt: Files to be Uploaded
        *Files to be Uploaded*

    #. GeoNode has the ability to restrict who can view, edit, and manage layers. On the right side of the page you can see the *Permission* section, where you can limit the access on your layer. Under **Who can view and download this data**, select **Any registered user**. This will ensure that *Anonymous* view access is disabled. In the same area, under **Who can edit this data**, select **your username**. This will ensure that *Only You* are able to edit the data in the layer.

        .. image:: img/permission.png
           :width: 250px
           :alt: Permissions
        *Permissions*

    #. To upload data, click the *Upload* button at the bottom.

    .. warning:: If the upload fails due to an e-mail issue, that means that the *GeoNode Notifications subsystem* must be disabled, since your VM most probably is not connected to the Internet and/or it is not able to send e-mail.
    
        .. code::
            
            $> cd /home/geonode/my_geonode/
            $> vim my_geonode/local_settings.py
            
                ...
                # notification settings
                NOTIFICATION_ENABLED = False
                ...
                # INSTALLED_APPS += (NOTIFICATIONS_MODULE, )
                ...
                #Define email service on GeoNode
                EMAIL_ENABLE = False
                ...
            
            $> sudo service apache2 restart
    
3. Create a new map
===================

The next step for you is to create a map and add the newly created layers to this map.

    #. Click the *Maps* link on the top toolbar. This will bring up the list of maps.

        .. image:: img/maps_quickstart.png
           :width: 500px
           :alt: Create new Map Button
        *Create new Map Button*

    #. Currently, there aren’t any maps here. To add one click the *Create a New Map* button and a map composition interface will display.

        .. image:: img/createmap1.png
           :width: 500px
           :alt: Maps Editor
        *Maps Editor*

        In this interface there is a toolbar, layer list, and map window. The map window contains the MapQuest OpenStreetMap layer by default.
        There are other service layers available here as well: Blue Marble, Bing Aerial With Labels, MapQuest, and OpenStreetMap.

    #. Click on the *New Layers* button and select *Add Layers*.

        .. image:: img/addlayerslink1.png
           :alt: Add Layers
        *Add Layers*

    #. Now you should be able to see all the availabel layers. In your case, this should only be the ones you’ve added before (San Andreas?).

    #. Select all of the layers by clicking the top entry and **Shift-clicking** the bottom one. Click *Add Layers* to add them all to the map.

        .. image:: img/addlayersselect_quickstart.png
           :alt: Add Layers
        *Add Layers*

    #. The layers will be added to the map. Click *Done* (right next to *Add Layers* at the bottom) to return to the main layers list.

    #. To save the map click on the Map button in the toolbar, and select *Save Map*.

        .. image:: img/savemaplink1.png
           :alt: Save Map
        *Save Map*

    #. Enter a title and abstract for your map.

        .. image:: img/savemapdialog1.png
           :alt: Edit Map Metadata
        *Edit Map Metadata*

    #. Click *Save*. Notice that the link on the top right of the page changed to reflect the map’s name.

        .. image:: img/mapname1.png
           :alt: Save Map
        *Save Map*

        This link contains a permalink to your map. If you open this link in a new window, your map will appear exactly as it was saved.

4. Share your map
=================

Now let’s publish our map and make it available to the world.

    #. Click the *Maps* link on the top toolbar. This will bring up the list of maps.

        .. image:: img/maps.png
           :width: 500px
           :alt: Explore Maps Button
        *Create new Map Button*

    #. Click on the *Map* To publish. Make any final adjustments to the map composition as desired, including zoom and pan settings.

    #. To save the map click on the Map button in the toolbar, and select *Publish Map*.

        .. image:: img/publishmap1.png
           :alt: Publish Map
        *Publish Map*
    
    #. The title and abstract as previously created should still be there. Make any adjustments as necessary, and click *Save*.

        .. image:: img/savemapdialog1.png
           :alt: Edit Map Metadata
        *Edit Map Metadata*

    #. A new dialog will appear with instructions on how to embed this map in a web page, including a code snippet. You can adjust the parameters as necessary.
    
        .. image:: img/publishmap2.png
           :alt: Embed the Map
        *Embed the Map*

    *Your map can now be shared by embedding it on any HTML!*

    .. note:: 
    
        Notice that you can easily retrieve the *full page* link of your *Map* by simply attaching the keyword ``embed`` at the end of the URL.
        
        As an instance if you open a *Map* and click on *View Map*, by just changing the URL
        
        from (this is just an example)
        
        .. code::
        
            http://localhost/maps/22/view
            
        to
        
        .. code::
        
            http://localhost/maps/22/embed
           
        You will get the full page view of your map

        .. image:: img/embed1.png
           :alt: Full Page View of the Map
        *Full Page View of the Map*
        
Try a small Exercise with HTML
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

    #. Open a text editor like shown in the figure below

        .. image:: img/html_embed1.png
           :alt: GEdit Text Editor
        *GEdit Text Editor*

    #. Write some very basic ``HTML`` code using the snippet provided by the *Publish Map* action above

        .. image:: img/html_embed2.png
           :alt: HTML Embedding the Map
        *HTML Embedding the Map*

        .. code:: html
        
            <html>
              <body>
                <H1>This is my new Map!</H1>
                <p align="center">
                  <iframe 
                       src="http://localhost/maps/22/embed"
                       width=800 
                       height=450
                       style="border:0">
                  </iframe>
                  <br>
                  <i>This is cool, isn't it?</i>
                </p>
              </body>
            </html>

    #. Click on *Save As* like shown in the figure below

        .. image:: img/html_embed3.png
           :alt: GEdit Text Editor - Save As
        *GEdit Text Editor - Save As*

    #. Save it to the *Desktop* by specifying a name and the extension ``.html``

        .. image:: img/html_embed4.png
           :alt: GEdit Text Editor - Save to Desktop
        *GEdit Text Editor - Save to Desktop*

    #. Click the *Show Desktop" button on the side bar, like shown below

        .. image:: img/html_embed5.png
           :alt: Show Desktop
        *Show Desktop*

    #. Double-Click on the file you just saved

        .. image:: img/html_embed6.png
           :alt: Double-Click on the HTML File
        *Double-Click on the HTML File*

    #. Look the results on the Web Browser, which will be automatically opened

        .. image:: img/html_embed7.png
           :alt: HTML Embedded Map
        *HTML Embedded Map*

To be continued
===============

Now you’ve gotten a quick insight in the possibilities of GeoNode. To learn more about GeoNode and its features, visit the official webpage `www.geonode.org <www.geonode.org>`_.

Stay in touch with the GeoNode community through the #geonode IRC channel using `http://webchat.freenode.net/ <http://webchat.freenode.net/>`_ or by asking your question in our `google group <https://groups.google.com/forum/#!forum/geonode-users>`_!

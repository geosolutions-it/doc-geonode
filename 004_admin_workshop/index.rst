.. _admin_workshop:

=======================
Administrators Workshop
=======================

Welcome to the GeoNode Training `Administrators Workshop` documentation v\ |release|.

This workshop will teach how to install and manage a deployment of the `GeoNode <http://geonode.org/>`_ software application.
At the end of this section you will master all the GeoNode sections and entities from an administrator perspective. 

You will know how to:

1. Use the GeoNode’s Django Administration Panel.
2. Use the console Management Commands for GeoNode.
3. Configure and customize your GeoNode installation.

*Prerequisites*

    Before proceeding with the reading, it is strongly recommended to be sure having clear the following concepts:

    1. GeoNode and Django framework concepts
    2. Good knowledge of Python
    3. Good knowledge of what is a geospatial server and geospatial web services.
    4. Good knowledge of what is metadata and catalog.
    5. Good knowledge of HTML and CSS.

.. toctree::
    :hidden:
    
    001_admin_panel/index
    004_customize_lookfeel/index
    
:ref:`admin_panel`
    GeoNode has an administration panel based on the Django admin which can be used to do some database operations.
    Although most of the operations can and should be done through the normal GeoNode interface, the admin panel provides a quick overview and management tool over the database.
    
:ref:`customize_admin`
    You might want to change the look of GeoNode, editing the colors and the logo of the website and adjust the templates for your needs. To do so, you first have to set up your own geonode project from a template. If you've successfully done this, you can go further and start theming your geonode project.

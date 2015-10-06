.. _dev_workshop:

===================
Developers Workshop
===================

Welcome to the GeoNode Training `Developers Workshop` documentation v\ |release|.

This workshop will teach how to develop with and for the `GeoNode <http://geonode.org>`_ software application.
This module will introduce you to the components that GeoNode is built with, the standards that it supports and the services it provides based on those standards, and an overview its architecture.

*Prerequisites*
    GeoNode is a web based GIS tool, and as such, in order to do development on GeoNode itself or to integrate it into your own application, you should be familiar with basic web development concepts as well as with general GIS concepts.

.. toctree::
    :hidden:
    
    001_devel_intro/index
    002_devel_core/index
    003_devel_env/index
    004_devel_debug/index

:ref:`intro_devel`
    This module will introduce you to the components that GeoNode is built with, the standards that it supports and the services it provides based on those standards, and an overview its architecture.

:ref:`dev_core`
    This module will introduce you to the basic tools and skills required to start actively developing GeoNode.

:ref:`install_devmode`
    This module shows a step-by-step guide for the setup of a GeoNode Develeopment Environent on an Ubuntu system.
    
    For other Linux distributions the commands are similar, the difference is mainly on the packages names.
    
    .. note:: For Windows: (:ref:`install_win_devmode`)
    
:ref:`dev_debug`
    GeoNode can be difficult to debug as there are several different components involved. This module shows some tecniques to debug the different parts of GeoNode.
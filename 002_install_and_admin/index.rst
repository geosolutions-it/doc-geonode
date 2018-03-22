.. _installation_and_admin:

====================
Installation & Admin
====================
Welcome to the GeoNode Training `Installation & Admin` documentation v\ |release|.

This module is more oriented to users having some System Administrator background.

At the end of this section you will be able to setup from scratch the whole GeoNode infrastructure and understand how to the different pieces are interconnected and which are their dependencies.

*Prerequisites*

    Before proceeding with the reading, it is strongly recommended to be sure having clear the following concepts:

    #. GeoNode and Django framework basic concepts
    #. What is Python
    #. What is a DBMS
    #. What is a Java Virtual Machine and the JDK
    #. Basic TCP/IP and networking concepts
    #. Linux OS basic shell and maintenance commands
    #. Apache HTTPD Server and WSGI Python bindings

.. toctree::
    :hidden:

    001_linux_admin_intro/index
    vm_setup_virtualbox.rst
    vm_running_vagrant.rst
    002_geonode_install/index
    003_geonode_update/index


:ref:`linux_admin_intro`
    This section describes how to setup a Virtual Machine running Ubuntu.

:ref:`geonode_install`
    This section will guide the user through the steps necessary to install GeoNode on Ubuntu.

:ref:`geonode_update`
    This section will guide the user through the steps necessary to update GeoNode from old versions.

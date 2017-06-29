.. _vm_running_vagrant.rst:

=========================
Running a VM with Vagrant
=========================

In this section you will find instructions on how to setup an Ubuntu 16.04 VM using
`Vagrant`_

.. _Vagrant: https://www.vagrantup.com/

Vagrant Setup
=============

#. Download and install latest version of `VirtualBox <http://download.virtualbox.org/virtualbox/5.1.22/>`_

#. Download `Vagrant <https://www.vagrantup.com/downloads>`_ from the official web `site <https://www.vagrantup.com/downloads>`_. Choose the installer matching your operating system and architecture.

Installation process is straightforward, refer to Vagrant `official documentation
<https://docs.vagrantup.com/v2/installation/index.html>`_ if you encounter any problem.

At the end of the installation process log out your system and log back
in.

Vagrant is going to need a `provider <https://docs.vagrantup.com/v2/providers/>`_
in order to setup the Virtual Machines. `VirtualBox` is supported out of the box.
Just make sure you install `one of the supported versions of VirtualBox <https://docs.vagrantup.com/v2/virtualbox>`_

Open a terminal and type `vagrant version`. A message containing the installed
version of Vagrant will be printed on the terminal

.. code-block:: bash

    Installed Version: 1.7.2
    Latest Version: 1.7.4
    ...

Virtual Machine Setup
=====================
Setup of a VM with a Vagrantfile assuming you are on unix.

Starting from a provided Vagrant BOX
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ mkdir geonode && cd geonode
    $ vagrant box add geonode-ws.box --name ubuntu/geonode-ws
    $ vagrant init ubuntu/geonode-ws

Edit the vagrantfile for more memory (see issue `#2076 <https://github.com/GeoNode/geonode/issues/2076>`_):

.. code-block:: bash

    config.ssh.username = "ubuntu"
    config.ssh.password = "ubuntu"
    config.vm.provider "virtualbox" do |vb|
        vb.memory = "4092"
    end

Starting from a Vagrant online image
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ mkdir geonode && cd geonode && vagrant init ubuntu/xenial64
    # Create a working directory and initialize the vagrantfile within

This will create a configuration file called `Vagrantfile` containing the settings
for the virtual machine, notably the `config.vm.box` variable set to "ubuntu/xenial64"
will tell Vagrant the specific VM we want to run (Ubuntu 16.04 "Xenial", 64 bit version)

Edit the vagrantfile for more memory (see issue `#2076 <https://github.com/GeoNode/geonode/issues/2076>`_):

.. code-block:: bash

    config.vm.provider "virtualbox" do |vb|
        vb.memory = "4092"
    end
    # Uncomment the section related to memory settings and edit that to at least 

Vagrant needs a VirtualBox installed on your system to start. Download it and install from VirtualBox.

With the latest bersions of VirtualBox, you may also need to update your Vagrant system. Download and install the latest version from Vagrant.

To start the VM, run

.. code-block:: bash

    $ vagrant up --provider=virtualbox
    # Start the virtual machine as defined into the vagrantfile

The first time you run the command it is going to take some since you do not have a
locally available image of the VM. Vagrant will download the VM from the `Vagrant
Could <https://vagrantcloud.com/>`_ to your local system.

.. code-block:: bash

    Bringing machine 'default' up with 'virtualbox' provider...
    ==> default: Box 'ubuntu/trusty32' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
    ==> default: Loading metadata for box 'ubuntu/trusty32'
    default: URL: https://atlas.hashicorp.com/ubuntu/trusty32
    ==> default: Adding box 'ubuntu/trusty32' (v20150928.0.0) for provider: virtualbox
    default: Downloading: https://atlas.hashicorp.com/ubuntu/boxes/trusty32/versions/20150928.0.0/providers/virtualbox.b
    ox
    default: Progress: 3% (Rate: 489k/s, Estimated time remaining: 0:11:11)))

At the end of the download process Vagrant will start the VM.

To access the Virtual machine, run

.. code-block:: bash

    $ vagrant ssh
    # Get into the box with the vagrant user

.. note::

    You need an SSH client for the previous command to work. Most Linux distributions
    come with an SSH installed. If you are using Windows as the guest operating system
    install MinGW or Cygwin or Git to obtain a command line SSH client. More information
    available `here <http://docs-v1.vagrantup.com/v1/docs/getting-started/ssh.html>`_

You will be connected to the guest Virtual Machine over SSH as user `vagrant`.

Install GeoNode into Ubuntu
===========================
From the `GeoNode documentation <http://docs.geonode.org/en/master/tutorials/install_and_admin/quick_install.html#linux>`_

.. code-block:: bash

    $ sudo add-apt-repository ppa:geonode/testing #only for workshop
    # vagrant is already into sudoers


.. code-block:: bash

    $ sudo apt-get update
    # update packages


.. code-block:: bash

    $ sudo apt-get install geonode
    # download of all required dependencies for installing GeoNode
    
.. warning:: The command ``sudo apt-get install geonode`` may create issues if your system ``locale`` is not correctly configured.

    In order to fix this, please follow the procedures below. If everything went well, you can skip those passages
    
    .. code-block:: bash
    
        $ sudo locale-gen en_US
        $ sudo locale-gen en_US.UTF-8
        $ sudo locale-gen it_IT.ISO-8859-1
        $ sudo locale-gen it_IT.UTF-8
        $ sudo locale-gen it_IT.ISO-8859-15@euro
        $ sudo update-locale
        # Update Locale
        
    .. code-block:: bash
    
        $ export LC_ALL=en_US.UTF-8
        $ export LANG=en_US.UTF-8
        $ export LANGUAGE=en_US.UTF-8
        $ sudo dpkg-reconfigure geonode
        # Export locale env vars and reconfigure geonode
        
Initialize GeoNode systemwide
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ geonode createsuperuser
    # a commandline toolkit has been installed systemwide so let's create the superuser of the GeoNode instance
    # use username: 'geonode' and password: 'geonode'

    $ sudo geonode-updateip localhost:8001
    # tell geonode which is its trusted address.

The ``geonode-updateip`` command will automatically fix GeoNode and GeoServer settings using the correct host and port.

**You have installed GeoNode! Congratulations!!!**

Anyway you don't have GeoNode available from your local browser. So we have to set a new forwarding port in the vagrantfile:

.. code-block:: bash

    config.vm.network "forwarded_port", guest: 80, host: 8001
    # This port has to be the same of that before and equal between guest and host. You can understand this later on.
    # Again don't use a port lower than 1024 on your host machine (not privileged)!!!

After this you have to reload the vagrant configuration:


.. code-block:: bash

    $ vagrant reload
    # reload vagrantfile with the new configuration
    
Behind the magic
================

Security hints and tricks
^^^^^^^^^^^^^^^^^^^^^^^^^
Security in GeoServer is hugely changed with the introduction of `OAuth2 <http://docs.geonode.org/en/master/tutorials/admin/geoserver_geonode_security/>`_

#. Be sure that the ports are available to your host machine

    .. code-block:: bash

        config.vm.network "forwarded_port", guest: 80, host: 8001
        # config.vm.network "forwarded_port", guest: 8080, host: 8080 # Optional,
        # enable if you don't need to reproduce a fully GeoNode proxied use case
        # forward another port to the host. You need to 'vagrant reload' to apply the changes
        
#. Double check that the correct GeoNode Base Url has been configure on GeoServer

    .. code-block:: bash

        $ sudo vi /usr/share/geoserver/data/security/role/geonode\ REST\ role\ service/config.xml
        # edit the configuration of geonode REST role service

    Must contain the following value:

        .. code-block:: xml

            <baseUrl>http://localhost:80/</baseUrl>
            <!-- base url of geonode web server -->

#. Set the correct OAuth2 server FQDN to GeoServer

    .. code-block:: bash

        $ sudo vi /usr/share/geoserver/data/security/filter/geonode-oauth2/config.xml
        # edit the configuration of GeoServer security for the oauth2 provider

    Make sure it contains these values:

        .. code-block:: xml

            <!-- GeoNode accessTokenUri -->
            <accessTokenUri>http://localhost/o/token/</accessTokenUri>
            <!-- GeoNode userAuthorizationUri -->
            <userAuthorizationUri>http://localhost:8001/o/authorize/</userAuthorizationUri>
            <!-- GeoServer Public URL -->
            <redirectUri>http://localhost:8001/geoserver</redirectUri>
            <!-- GeoNode checkTokenEndpointUrl -->
            <checkTokenEndpointUrl>http://localhost/api/o/v4/tokeninfo/</checkTokenEndpointUrl>
            <!-- GeoNode logoutUri -->
            <logoutUri>http://localhost:8001/account/logout/</logoutUri>

#. Set the correct OAuth2 Keys to GeoServer

    .. code-block:: bash
    
        $ sudo vi /usr/share/geoserver/data/security/filter/geonode-oauth2/config.xml
        # edit the configuration of GeoServer security for the oauth2 provider

    Make sure the clientId and clientSecret keys are the same of the GeoNode ones:

        .. code-block:: xml
        
            <!-- GeoNode OAuth2 Client ID -->
            <cliendId>the_geonode_oauth_client_id</cliendId>
            <!-- GeoNode OAuth2 Client Secret -->
            <clientSecret>the_geonode_oauth_client_secret</clientSecret>

#. Check the settings of GeoServer Global Proxy Base and Default Authentication Provider

    .. code-block:: bash
    
        $ sudo vi /usr/share/geoserver/data/security/auth/geonodeAuthProvider/config.xml
        # edit configuration of default GeoServer authentication provider
        $ sudo vi /usr/share/geoserver/data/global.xml
        # edit configuration of default GeoServer proxy base url

    Change with these values:

        .. code-block:: xml
        
            <baseUrl>http://localhost:8001/</baseUrl>
            <!-- base url of geonode web server -->

    Leave the default for this vagrant configuration:

        .. code-block:: xml
        
            <proxyBaseUrl>http://localhost:80/geoserver</proxyBaseUrl>
            <!-- proxy base url of geonode web server -->
            <!-- set the port to 8080 if you are forwarding that to the host -->

#. Restart the server:

    .. code-block:: bash
    
        $ sudo service tomcat7 restart
        # restart Tomcat

Open GeoNode GUI at http://localhost:8001/

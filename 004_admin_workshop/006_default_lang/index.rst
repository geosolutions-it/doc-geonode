.. _default_language:

=============================
Changing the Default Language
=============================

GeoNode's default language is English, but GeoNode users can change the interface language with the pulldown menu at the top-right of most GeoNode pages.  Once a user selects a language GeoNode remembers that language for subsequent pages.

GeoNode Configuration
=====================

As root edit the geonode config file :file:`/home/geonode/geonode/geonode/settings.py` (or :file:`/etc/geonode/settings.py` if GeoNode has been installed using **apt-get**) and change ``LANGUAGE_CODE`` to the desired default language.

.. note:: A list of language codes can be found in the global django config file :file:`/usr/local/lib/python2.7/dist-packages/django/conf/global_settings.py` (or :file:`/var/lib/geonode/lib/python2.7/site-packages/django/conf/global_settings.py` if GeoNode has been installed using **apt-get**).

For example, to make French the default language use
::

    LANGUAGE_CODE = 'fr'

Unfortunately  Django overrides this setting, giving the language setting of a user's browser priority.  For example, if ``LANGUAGE_CODE`` is set to French, but the user has configured their operating system for Spanish they may see the Spanish version when they first visit GeoNode.


Additional Steps
================

If this is not the desired behaviour, and all users should initially see the default ``LANGUAGE_CODE``, regardless of their browser's settings, do the following steps to ensure Django ignores the browser language settings.  (Users can always use the pulldown language menu to change the language at any time.)

As **root** create a new directory within GeoNode's site packages

    .. code::

        mkdir /usr/lib/python2.7/dist-packages/setmydefaultlanguage

or

    .. code::

        mkdir /var/lib/geonode/lib/python2.7/site-packages/setmydefaultlanguage

or

    .. code::

        mkdir ~/.venvs/geonode/lib/python2.7/site-packages/setmydefaultlanguage

if GeoNode has been installed using **apt-get** as root create and edit a new file :file:`/usr/lib/python2.7/dist-packages/setmydefaultlanguage/__init__.py`

if GeoNode has been **installed as a custom project dependency**, :file:`~/.venvs/geonode/lib/python2.7/site-packages/setmydefaultlanguage/__init__.py`

Add the following lines

    .. code:: python
    
        from django.conf import settings
        from django.utils import translation

        class ForceDefaultLanguageMiddleware(object):
            """
            Ignore Accept-Language HTTP headers

            This will force the I18N machinery to always choose settings.LANGUAGE_CODE
            as the default initial language, unless another one is set via sessions or cookies

            Should be installed *before* any middleware that checks request.META['HTTP_ACCEPT_LANGUAGE'],
            namely django.middleware.locale.LocaleMiddleware
            """
            def process_request(self, request):
                request.LANG = getattr(settings, 'LANGUAGE_CODE', settings.LANGUAGE_CODE)
                translation.activate(request.LANG)
                request.LANGUAGE_CODE = request.LANG

At the end of the GeoNode configuration file :file:`/home/geonode/geonode/geonode/settings.py`, :file:`/etc/geonode/settings.py` if GeoNode has been installed using **apt-get** or :file:`~/.venvs/geonode/src/geonode/geonode/settings.py` if **installed as a custom project dependency**, add the following lines to ensure the above class is executed::

    MIDDLEWARE_CLASSES += (
        'setmydefaultlanguage.ForceDefaultLanguageMiddleware',
    )

Restart
=======

Finally restart Apache2 as root with::

    service apache2 restart


Please refer to Translating GeoNode for information on editing GeoNode pages in different languages and create new GeoNode Translations. 

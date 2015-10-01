.. _getting_started_w_django:

===========================
Getting Started With Django
===========================

Object-relational mapper
========================
Deﬁne your data models entirely in Python. You get a rich, dynamic database-access API for free — but you can still write SQL if needed.

.. code-block:: python
    :linenos:
    
    class Band(models.Model):
        """A model of a rock band."""
        name = models.CharField(max_length=200)
        can_rock = models.BooleanField(default=True)


    class Member(models.Model):
        """A model of a rock band member."""
        name = models.CharField("Member's name", max_length=200)
        instrument = models.CharField(choices=(
                ('g', "Guitar"),
                ('b', "Bass"),
                ('d', "Drums"),
            ),
            max_length=1
        )
        band = models.ForeignKey("Band")
        
Models
------

A model is the single, definitive source of information about your data. It contains the essential fields and behaviors of the data you’re storing. Generally, each model maps to a single database table.

The basics:

* Each model is a Python class that subclasses ``django.db.models.Model``.
* Each attribute of the model represents a database field.
* With all of this, Django gives you an automatically-generated database-access API; see :ref:`making_queries`.

Quick example
.............

This example model defines a ``Person``, which has a **first_name** and **last_name**:

.. code-block:: python
    :linenos:

    from django.db import models

    class Person(models.Model):
        first_name = models.CharField(max_length=30)
        last_name = models.CharField(max_length=30)

**first_name** and **last_name** are fields of the model. Each field is specified as a class attribute, and each attribute maps to a database column.

The above ``Person`` model would create a database table like this:

.. code-block:: sql
    :linenos:
    
    CREATE TABLE myapp_person (
        "id" serial NOT NULL PRIMARY KEY,
        "first_name" varchar(30) NOT NULL,
        "last_name" varchar(30) NOT NULL
    );
    
Some technical notes:

* The name of the table, **myapp_person**, is automatically derived from some model metadata but can be overridden.
* An **id** field is added automatically, but this behavior can be overridden.
* The **CREATE TABLE** SQL in this example is formatted using PostgreSQL syntax, but it’s worth noting Django uses SQL tailored to the database backend specified in your settings file.

Using models
............

Once you have defined your models, you need to tell Django you’re going to use those models. Do this by editing your settings file and changing the **INSTALLED_APPS** setting to add the name of the module that contains your **models.py**.

For example, if the models for your application live in the module **myapp.models** (the package structure that is created for an application by the **manage.py startapp** script), **INSTALLED_APPS** should read, in part:

.. code-block:: python
    :linenos:

    INSTALLED_APPS = (
        #...
        'myapp',
        #...
    )

When you add new apps to **INSTALLED_APPS**, be sure to run **manage.py migrate**, optionally making migrations for them first with **manage.py makemigrations**.

.. note:: GeoNode has specific commands and shortcuts wich can be called through the **manage.py** class

Fields
......

The most important part of a model – and the only required part of a model – is the list of database fields it defines. Fields are specified by class attributes. 
Be careful not to choose field names that conflict with the models API like **clean**, **save**, or **delete**.

Example:

.. code-block:: python
    :linenos:

    from django.db import models

    class Musician(models.Model):
        first_name = models.CharField(max_length=50)
        last_name = models.CharField(max_length=50)
        instrument = models.CharField(max_length=100)

    class Album(models.Model):
        artist = models.ForeignKey(Musician)
        name = models.CharField(max_length=100)
        release_date = models.DateField()
        num_stars = models.IntegerField()

More: `Field Types <https://docs.djangoproject.com/en/1.8/topics/db/models/#field-types>`_

Model methods
.............

Define custom methods on a model to add custom “row-level” functionality to your objects.
Whereas **Manager** methods are intended to do “table-wide” things, model methods should act on a particular model instance.

This is a valuable technique for keeping business logic in one place – the model.

For example, this model has a few custom methods:

.. code-block:: python
    :linenos:

    from django.db import models

    class Person(models.Model):
        first_name = models.CharField(max_length=50)
        last_name = models.CharField(max_length=50)
        birth_date = models.DateField()

        def baby_boomer_status(self):
            "Returns the person's baby-boomer status."
            import datetime
            if self.birth_date < datetime.date(1945, 8, 1):
                return "Pre-boomer"
            elif self.birth_date < datetime.date(1965, 1, 1):
                return "Baby boomer"
            else:
                return "Post-boomer"

        def _get_full_name(self):
            "Returns the person's full name."
            return '%s %s' % (self.first_name, self.last_name)
        full_name = property(_get_full_name)

The last method in this example is a `property <https://docs.djangoproject.com/en/1.8/glossary/#term-property>`_.

The `model instance reference <https://docs.djangoproject.com/en/1.8/ref/models/instances/>`_ has a complete list of `methods automatically given to each model <https://docs.djangoproject.com/en/1.8/ref/models/instances/#model-instance-methods>`_.
You can override most of these – see `overriding predefined model methods <https://docs.djangoproject.com/en/1.8/topics/db/models/#overriding-predefined-model-methods>`_

More: `Models Methods <https://docs.djangoproject.com/en/1.8/topics/db/models/#model-methods>`_

Model inheritance
.................

Model inheritance in Django works almost identically to the way normal class inheritance works in Python, but the basics at the beginning of the page should still be followed. 
That means the base class should subclass ``django.db.models.Model``.

The only decision you have to make is whether you want the parent models to be models in their own right (with their own database tables), or if the parents are just holders of common information that will only be visible through the child models.

There are three styles of inheritance that are possible in Django.

1. Often, you will just want to use the parent class to hold information that you don’t want to have to type out for each child model. This class isn’t going to ever be used in isolation, so `Abstract base classes <https://docs.djangoproject.com/en/1.8/topics/db/models/#abstract-base-classes>`_ are what you’re after.
2. If you’re subclassing an existing model (perhaps something from another application entirely) and want each model to have its own database table, `Multi-table inheritance <https://docs.djangoproject.com/en/1.8/topics/db/models/#multi-table-inheritance>`_ is the way to go.
3. Finally, if you only want to modify the Python-level behavior of a model, without changing the models fields in any way, you can use `Proxy models <https://docs.djangoproject.com/en/1.8/topics/db/models/#proxy-models>`_.

More: `Model Inheritance <https://docs.djangoproject.com/en/1.8/topics/db/models/#model-inheritance>`_

.. _making_queries:

`Making queries`_
-----------------

Once you’ve created your data models, Django automatically gives you a database-abstraction API that lets you create, retrieve, update and delete objects.

Throughout this guide (and in the reference), we’ll refer to the following models, which comprise a Weblog application:

.. code-block:: python
    :linenos:

    from django.db import models

    class Blog(models.Model):
        name = models.CharField(max_length=100)
        tagline = models.TextField()

        def __str__(self):              # __unicode__ on Python 2
            return self.name

    class Author(models.Model):
        name = models.CharField(max_length=50)
        email = models.EmailField()

        def __str__(self):              # __unicode__ on Python 2
            return self.name

    class Entry(models.Model):
        blog = models.ForeignKey(Blog)
        headline = models.CharField(max_length=255)
        body_text = models.TextField()
        pub_date = models.DateField()
        mod_date = models.DateField()
        authors = models.ManyToManyField(Author)
        n_comments = models.IntegerField()
        n_pingbacks = models.IntegerField()
        rating = models.IntegerField()

        def __str__(self):              # __unicode__ on Python 2
            return self.headline

Creating objects
................

To represent database-table data in Python objects, Django uses an intuitive system: 
A model class represents a database table, and an instance of that class represents a particular record in the database table.

To create an object, instantiate it using keyword arguments to the model class, then call **save()** to save it to the database.

Assuming models live in a file **mysite/blog/models.py**, here’s an example:

.. code-block:: python
    :linenos:

    >>> from blog.models import Blog
    >>> b = Blog(name='Beatles Blog', tagline='All the latest Beatles news.')
    >>> b.save()

This performs an **INSERT SQL** statement behind the scenes. Django doesn’t hit the database until you explicitly call **save()**.

The **save()** method has no return value.

Saving changes to objects
.........................

To save changes to an object that’s already in the database, use **save()**.

Given a Blog instance b5 that has already been saved to the database, this example changes its name and updates its record in the database:

.. code-block:: python
    :linenos:

    >>> b5.name = 'New name'
    >>> b5.save()

This performs an **UPDATE SQL** statement behind the scenes. Django doesn’t hit the database until you explicitly call **save()**.

Retrieving objects
..................

To retrieve objects from your database, construct a **QuerySet** via a **Manager** on your model class.

A **QuerySet** represents a collection of objects from your database. It can have zero, one or many filters. Filters narrow down the query results based on the given parameters. 
In SQL terms, a **QuerySet** equates to a **SELECT** statement, and a filter is a limiting clause such as **WHERE** or **LIMIT**.

You get a **QuerySet** by using your model’s **Manager**. Each model has at least one **Manager**, and it’s called ``objects`` by default. Access it directly via the model class, like so:

.. code-block:: python
    :linenos:

    >>> Blog.objects
    <django.db.models.manager.Manager object at ...>
    >>> b = Blog(name='Foo', tagline='Bar')
    >>> b.objects
    Traceback:
        ...
    AttributeError: "Manager isn't accessible via Blog instances."
    Note

**Managers** are accessible only via model classes, rather than from model instances, to *enforce a separation* between “table-level” operations and “record-level” operations.
The **Manager** is the main source of **QuerySets** for a model. For example, ``Blog.objects.all()`` returns a **QuerySet** that contains all Blog objects in the database.

Retrieving all objects
......................

The simplest way to retrieve objects from a table is to get all of them. To do this, use the **all()** method on a **Manager**:

.. code-block:: python
    :linenos:

    >>> all_entries = Entry.objects.all()

The **all()** method returns a **QuerySet** of all the objects in the database.

Retrieving specific objects with filters
........................................

The **QuerySet** returned by **all()** describes all objects in the database table. Usually, though, you’ll need to select only a subset of the complete set of objects.

To create such a subset, you refine the initial **QuerySet**, adding filter conditions. The two most common ways to refine a **QuerySet** are:

**filter(**kwargs)**

Returns a new **QuerySet** containing objects that match the given lookup parameters.

**exclude(**kwargs)**

Returns a new **QuerySet** containing objects that do not match the given lookup parameters.
The lookup parameters (****kwargs** in the above function definitions) should be in the format described in **Field** lookups below.

For example, to get a **QuerySet** of blog entries from the year 2006, use **filter()** like so:

.. code-block:: python
    :linenos:

    Entry.objects.filter(pub_date__year=2006)

With the default manager class, it is the same as:

.. code-block:: python
    :linenos:

    Entry.objects.all().filter(pub_date__year=2006)

Chaining filters
................

The result of refining a **QuerySet** is itself a **QuerySet**, so it’s possible to chain refinements together. 
For example:

.. code-block:: python
    :linenos:

    >>> Entry.objects.filter(
    ...     headline__startswith='What'
    ... ).exclude(
    ...     pub_date__gte=datetime.date.today()
    ... ).filter(
    ...     pub_date__gte=datetime(2005, 1, 30)
    ... )

This takes the initial **QuerySet** of all entries in the database, adds a filter, then an exclusion, then another filter. 
The final result is a **QuerySet** containing all entries with a headline that starts with “What”, that were published between January 30, 2005, and the current day.

More: `Making queries <https://docs.djangoproject.com/en/1.8/topics/db/queries/#making-queries>`_

URLs and views
==============

A clean, elegant URL scheme is an important detail in a high-quality Web application. Django encourages beautiful URL design and doesn’t put any cruft in URLs, like .php or .asp.

To design URLs for an application, you create a Python module called a URLconf. Like a table of contents for your app, it contains a simple mapping between URL patterns and your views.

.. code-block:: python
    :linenos:

    from django.conf.urls import url
    from . import views

    urlpatterns = [
        url(r'^bands/$', views.band_listing, name='band-list'),
        url(r'^bands/(\d+)/$', views.band_detail, name='band-detail'),
        url(r'^bands/search/$', views.band_search, name='band-search'),
    ]
      
.. code-block:: python
    :linenos:

    from django.shortcuts import render

    def band_listing(request):
        """A view of all bands."""
        bands = models.Band.objects.all()
        return render(request, 'bands/band_listing.html', {'bands': bands})

More: `URL dispatcher <https://docs.djangoproject.com/en/1.8/topics/http/urls/>`_

Templates
=========

Django’s template language is designed to strike a balance between power and ease. It’s designed to feel comfortable and easy-to-learn to those used to working with HTML, like designers and front-end developers. But it is also flexible and highly extensible, allowing developers to augment the template language as needed.

.. code-block:: html
    :linenos:

    <html>
      <head>
        <title>Band Listing</title>
      </head>
      <body>
        <h1>All Bands</h1>
        <ul>
        {% for band in bands %}
          <li>
            <h2><a href="{{ band.get_absolute_url }}">{{ band.name }}</a></h2>
            {% if band.can_rock %}<p>This band can rock!</p>{% endif %}
          </li>
        {% endfor %}
        </ul>
      </body>
    </html>
    
More: `Templates <https://docs.djangoproject.com/en/1.8/topics/templates/>`_

Forms
=====

Django provides a powerful form library that handles rendering forms as HTML, validating user-submitted data, and converting that data to native Python types. Django also provides a way to generate forms from your existing models and use those forms to create and update data.

.. code-block:: python
    :linenos:

    from django import forms

    class BandContactForm(forms.Form):
        subject = forms.CharField(max_length=100)
        message = forms.CharField()
        sender = forms.EmailField()
        cc_myself = forms.BooleanField(required=False)
    
GET and POST
------------

**GET** and **POST** are the only HTTP methods to use when dealing with forms.

Django’s login form is returned using the **POST** method, in which the browser bundles up the form data, encodes it for transmission, sends it to the server, and then receives back its response.

**GET**, by contrast, bundles the submitted data into a string, and uses this to compose a URL. The URL contains the address where the data must be sent, as well as the data keys and values. 
You can see this in action if you do a search in the Django documentation, which will produce a URL of the form ``https://docs.djangoproject.com/search/?q=forms&release=1``.

**GET** and **POST** are typically used for different purposes.

Any request that could be used to change the state of the system - for example, a request that makes changes in the database - should use **POST**. **GET** should be used only for requests that do not affect the state of the system.

**GET** would also be unsuitable for a password form, because the password would appear in the URL, and thus, also in browser history and server logs, all in plain text. Neither would it be suitable for large quantities of data, or for binary data, such as an image. A Web application that uses **GET** requests for admin forms is a security risk: it can be easy for an attacker to mimic a form’s request to gain access to sensitive parts of the system. **POST**, coupled with other protections like Django’s CSRF protection offers more control over access.

On the other hand, **GET** is suitable for things like a web search form, because the URLs that represent a **GET** request can easily be bookmarked, shared, or resubmitted.
    
More: `Working With Forms <https://docs.djangoproject.com/en/1.8/topics/forms/#working-with-forms>`_

Authentication
==============

Django comes with a full-featured and secure authentication system. 
It handles user accounts, groups, permissions and cookie-based user sessions. This lets you easily build sites that let users to create accounts and safely log in/out.

.. code-block:: python
    :linenos:

    from django.contrib.auth.decorators import login_required
    from django.shortcuts import render

    @login_required
    def my_protected_view(request):
        """A view that can only be accessed by logged-in users"""
        return render(request, 'protected.html', {'current_user': request.user})

More: `User authentication in Django <https://docs.djangoproject.com/en/stable/topics/auth/>`_
    
Admin
=====

One of the most powerful parts of Django is its automatic admin interface. 
It reads metadata in your models to provide a powerful and production-ready interface that content producers can immediately use to start managing content on your site. 
It’s easy to set up and provides many hooks for customization.

.. code-block:: python
    :linenos:
    
    from django.contrib import admin
    from bands.models import Band, Member

    class MemberAdmin(admin.ModelAdmin):
        """Customize the look of the auto-generated admin for the Member model"""
        list_display = ('name', 'instrument')
        list_filter = ('band',)

    admin.site.register(Band)  # Use the default options
    admin.site.register(Member, MemberAdmin)  # Use the customized options

.. note:: The advanced workshop for Developers will provide more details on GeoNode specific models and admin interface

More: `The Django admin site <https://docs.djangoproject.com/en/stable/ref/contrib/admin/>`_

Internationalization
====================

Django offers full support for translating text into different languages, plus locale-specific formatting of dates, times, numbers and time zones. 
It lets developers and template authors specify which parts of their apps should be translated or formatted for local languages and cultures, 
and it uses these hooks to localize Web applications for particular users according to their preferences.

.. code-block:: python
    :linenos:

    from django.shortcuts import render
    from django.utils.translation import ugettext

    def homepage(request):
        """
        Shows the homepage with a welcome message that is translated in the
        user's language.
        """
        message = ugettext('Welcome to our site!')
        return render(request, 'homepage.html', {'message': message})

.. code-block:: html
    :linenos:
    
    {% load i18n %}
    <html>
      <head>
        <title>{% trans 'Homepage - Hall of Fame' %}</title>
      </head>
      <body>
        {# Translated in the view: #}
        <h1>{{ message }}</h1>
        <p>
          {% blocktrans count member_count=bands.count %}
          Here is the only band in the hall of fame:
          {% plural %}
          Here are all the {{ member_count }} bands in the hall of fame:
          {% endblocktrans %}
        </p>
        <ul>
        {% for band in bands %}
          <li>
            <h2><a href="{{ band.get_absolute_url }}">{{ band.name }}</a></h2>
            {% if band.can_rock %}<p>{% trans 'This band can rock!' %}</p>{% endif %}
          </li>
        {% endfor %}
        </ul>
      </body>
    </html>

.. note:: The advanced workshop for Developers will provide more details on how to create languages and translations on GeoNode using `Transifex <https://www.transifex.com/>`_

More: `Internationalization and localization <https://docs.djangoproject.com/en/stable/topics/i18n/>`_

Security
========

Django provides multiple protections against:

* Clickjacking
    Clickjacking is a type of attack where a malicious site wraps another site in a frame. This attack can result in an unsuspecting user being tricked into performing unintended actions on the target site.
    
    The **X-Frame-Options middleware** contained in a form allow a supporting browser to prevent a site from being rendered inside a frame
    
* Cross site scripting (XSS)
    XSS attacks allow a user to inject client side scripts into the browsers of other users. This is usually achieved by storing the malicious scripts in the database where it will be retrieved and displayed to other users, or by getting users to click a link which will cause the attacker’s JavaScript to be executed by the user’s browser. However, XSS attacks can originate from any untrusted source of data, such as cookies or Web services, whenever the data is not sufficiently sanitized before including in a page.
    
* Cross site request forgery (CSRF)
    CSRF attacks allow a malicious user to execute actions using the credentials of another user without that user’s knowledge or consent.
    
    CSRF protection works by checking for a nonce in each POST request. This ensures that a malicious user cannot simply “replay” a form POST to your Web site and have another logged in user unwittingly submit that form. The malicious user would have to know the nonce, which is user specific (using a cookie).
    
* SQL injection
    SQL injection is a type of attack where a malicious user is able to execute arbitrary SQL code on a database. This can result in records being deleted or data leakage.
    
* Host header validation
    Django uses the **Host** header provided by the client to construct URLs in certain cases. 
    While these values are sanitized to prevent Cross Site Scripting attacks, a fake **Host** value can be used for Cross-Site Request Forgery, cache poisoning attacks, and poisoning links in emails.

    Because even seemingly-secure web server configurations are susceptible to fake **Host** headers, Django validates **Host** headers against the **ALLOWED_HOSTS** setting in the ``django.http.HttpRequest.get_host()`` method.

    This validation only applies via **get_host()**; if your code accesses the Host header directly from **request.META** you are bypassing this security protection.

* SSL/HTTPS
    It is always better for security, though not always practical in all cases, to deploy your site behind HTTPS. Without this, it is possible for malicious network users to sniff authentication credentials or any other information transferred between client and server, and in some cases – active network attackers – to alter data that is sent in either direction.
    
    Django provides some settings to secure your site unser SSL/HTTPS.

.. warning::
    While Django provides good security protection out of the box, it is still important to properly deploy your application and take advantage of the security protection of the Web server, operating system and other components.

    * Make sure that your Python code is outside of the Web server’s root. This will ensure that your Python code is not accidentally served as plain text (or accidentally executed).
    * Take care with any user uploaded files.
    * Django does not throttle requests to authenticate users. To protect against brute-force attacks against the authentication system, you may consider deploying a Django plugin or Web server module to throttle these requests.
    * Keep your **SECRET_KEY** a secret.
    * It is a good idea to limit the accessibility of your caching system and database using a firewall.

More: `Security in Django <https://docs.djangoproject.com/en/stable/topics/security/>`_

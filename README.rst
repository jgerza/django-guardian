===============
django-guardian
===============

.. image:: https://travis-ci.org/django-guardian/django-guardian.svg?branch=devel
  :target: https://travis-ci.org/django-guardian/django-guardian

``django-guardian`` is implementation of per object permissions [1]_ as 
authorization backend which is supported since Django_ 1.5. It won't
work with older Django_ releases.

Documentation
-------------

Online documentation is available at https://django-guardian.readthedocs.io/.

Installation
------------

To install ``django-guardian`` simply run::

    pip install django-guardian

Configuration
-------------

We need to hook ``django-guardian`` into our project.

1. Put ``guardian`` into your ``INSTALLED_APPS`` at settings module::

      INSTALLED_APPS = (
         ...
         'guardian',
      )
   
2. Add extra authorization backend to your `settings.py`::

      AUTHENTICATION_BACKENDS = (
          'django.contrib.auth.backends.ModelBackend', # default
          'guardian.backends.ObjectPermissionBackend',
      )

4. Create ``guardian`` database tables by running::

     python manage.py migrate

Usage
-----

After installation and project hooks we can finally use object permissions
with Django_.

Lets start really quickly::

    >>> from django.contrib.auth.models import User, Group
    >>> jack = User.objects.create_user('jack', 'jack@example.com', 'topsecretagentjack')
    >>> admins = Group.objects.create(name='admins')
    >>> jack.has_perm('change_group', admins)
    False
    >>> from guardian.models import UserObjectPermission
    >>> UserObjectPermission.objects.assign_perm('change_group', user=jack, obj=admins)
    <UserObjectPermission: admins | jack | change_group>
    >>> jack.has_perm('change_group', admins)
    True

Of course our agent jack here would not be able to *change_group* globally::

    >>> jack.has_perm('change_group')
    False

Admin integration
-----------------

Replace ``admin.ModelAdmin`` with ``GuardedModelAdmin`` for those models
which should have object permissions support within admin panel.

For example::

    from django.contrib import admin
    from myapp.models import Author
    from guardian.admin import GuardedModelAdmin

    # Old way:
    #class AuthorAdmin(admin.ModelAdmin):
    #    pass

    # With object permissions support
    class AuthorAdmin(GuardedModelAdmin):
        pass

    admin.site.register(Author, AuthorAdmin)


.. [1] Great paper about this feature is available at `djangoadvent articles <https://github.com/djangoadvent/djangoadvent-articles/blob/master/1.2/06_object-permissions.rst>`_.

.. _Django: http://www.djangoproject.com/


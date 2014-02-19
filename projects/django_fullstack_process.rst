Django Development and Deployment
+++++++++++++++++++++++++++++++++

:tags: django dev
:summary: Notes that are useful for building, maintaining, deploying Django 
          projects


The project
===========

settings
--------

::

    . manage.py
    - settings/
    |___. dev.py
    |___. prod.py
    |___. test.py

De-couple the secrets from the code.

Use `environ.get` to get secrets from environment variables


Environment Variables
---------------------

`DJANGO_SETTINGS_MODULE` : used by default to tell Django where settings are. 
The default, defined in `manage.py` with:::
    
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "project.settings.dev")


`DATABASE_URL` : scheme://... . Set default in `dev.py`:::
    
    environ.setdefault("DATABASE_URL",
                       "sqlite:///" + join(SITE_ROOT, "db.sqlite")
    


`SECRET_KEY` : ...

(designate var(s) for caches)

(designate var(s) for email settings)


virtualenv
``````````

Most of the dev and tests will happen using `manage.py`. Why not set up 
virtualenvs to set env variables appropriate to different use cases.

set env variables using ``~/.virtualenvs/env/bin/postactivate`` and unset with
``~/.virtualenvs/env/bin/postdeactivate`` hook scripts

uWSGI
`````

Will be using it in production. use `--env` flag or 

::

    -env DJANGO_SETTINGS_



manage.py
---------

...


life cycle defenitions
----------------------

dev
```

...

test_local
``````````

...

test_prod
`````````

...

prod
````

....


Developping
===========

Quality
-------

PyLint, pep8, PyFlakes

Geany
-----

This is a simple, solid, extensible IDE

http://wiki.geany.org/howtos/check_python_code for quick and dirty integration
of code quality tools

Deploying
=========

...

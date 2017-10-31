Development
===========

To start with, clone the repo to your local machine ::

    $ git clone git@github.com:caktus/tequila-django.git
    $ cd tequila-django/

Then, within some existing project, uninstall the pinned version of
tequila-django and replace it with a symlink to the checkout.  The
following assumes that you have Ansible configured to install roles
into the deployment/roles/ directory within your Django project's tree
::

    $ cd my-django-project/
    $ workon project
    (project)$ ansible-galaxy uninstall tequila-django
    (project)$ cd deployment/roles/
    (project)$ ln -s /path/to/tequila-django

Now when you make changes to the tasks or files in your checkout of
tequila-django, those changes will be immediately available when you
do a deployment.

When finished trying out your changes, be sure to remove the symlink
and reinstall the pinned version ::

    (project)$ rm deployment/roles/tequila-django
    (project)$ ansible-galaxy install -r deployment/requirements.yml


Documentation
-------------

In order to build the documentation, you need to have `Sphinx
<http://www.sphinx-doc.org/en/stable/>`_ and its dependencies
installed ::

    $ mkvirtualenv tequila -p $(which python3)
    (tequila)$ pip install Sphinx

In the docs/ directory, you can now build the html version of the docs
using the provided Makefile ::

    (tequila)$ cd tequila-django/docs/
    (tequila)$ make html

This will create or update the html files in docs/_build/html/.  The
easiest way to then view the updated documentation is by using Python
as a simple webserver ::

    (tequila)$ python -m http.server 8000

While this is running, you can point your browser at
`http://localhost:8000/_build/html/
<http://localhost:8000/_build/html/>`_ to browse the docs.

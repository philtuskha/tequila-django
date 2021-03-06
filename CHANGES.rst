Tequila-django
==============

Changes

v 0.9.7 on Oct 19, 2017
-----------------------

* Work around for Ansible bug #25431. Store Github deploy key in a fact
  so it's available regardless of result of previous task.


v 0.9.6 on Sep 21, 2017
-----------------------

* Added an ``additional_domains`` variable, to allow for the
  multi-domain case to be supported in the Django settings file.


v 0.9.5 on Sep 13, 2017
-----------------------

* Added a ``cloud_staticfiles`` boolean variable, that determines
  whether to run ``collectstatic`` only once or not.


v 0.9.4 on Sep 6, 2017
----------------------

* Mark the Django migration task to only run once.


v 0.9.3 on Aug 28, 2017
-----------------------

* Allow Git checkout of source tree on Vagrant environments instead
  of only supporting source_is_local=true.

* Ignore .env and node_modules when rsync-ing source tree for
  source_is_local processing.


v 0.9.2 on Aug 17, 2017
-----------------------

* Adjustment to the Django migrate task to improve its speed, and a
  suggestion for a project-wide setting to improve it further.


v 0.9.1 on July 20, 2017
------------------------

* Convert the collectstatic task into a handler.  This is needed so
  that it happens after the ``npm run build`` step, so that the files
  generated from that are included.


v 0.9.0 on July 18, 2017
------------------------

* Remove the nodejs installation and package management in favor of
  the geerlingguy/nodejs Ansible role.

  .. IMPORTANT::

     To upgrade to this version, you will have to make the following
     changes to your deployment files.

     1. Add the geerlingguy/nodejs role to
        deployment/requirements.yml, and bump the version of
        tequila-django::

          ---
          - src: https://github.com/caktus/tequila-django
            version: v0.9.3
            name: tequila-django

          - src: geerlingguy.nodejs
            version: 4.1.2
            name: nodejs
          ...

     #. Install the new role, and make sure that tequila-django gets
        upgraded.  Since ``ansible-galaxy`` does not at this time seem
        to have support for version upgrades, either explicitly remove
        the tequila-django directory from deployment/roles/, or use
        ``ansible-galaxy uninstall tequila-django``, before running
        the command to install the roles from the requirements.yml
        file.

     #. Include the configuration variables for geerlingguy/nodejs in
        your project-wide variables file (usually
        deployment/playbooks/group_vars/all/project.yml)::

          ---
          nodejs_version: "6.x"
          nodejs_install_npm_user: "{{ project_name }}"
          nodejs_package_json_path: "{{ source_dir }}"
          nodejs_config_unsafe_perm: true

        If you previously had anything configured under the variable
        ``global_npm_installs``, rename this variable to
        ``nodejs_npm_global_packages``.  Note that
        ``nodejs_config_unsafe_perm`` has to be set to ``true`` in
        order for the global npm installs to work for
        ``nodejs_install_npm_user`` set to anything other than root.

     #. If you previously had a different version of nodejs installed
        using a .deb package, you should probably uninstall it, and
        remove any PPA source file associated with it (if used).

     #. Modify your deployment/playbooks/web.yml file (or equivalent)
        to include the nodejs role _after_ the tequila-django role::

          ---
          - hosts: web
            become: yes
            roles:
              - tequila-nginx
              - { role: tequila-django, is_web: true }
              - nodejs

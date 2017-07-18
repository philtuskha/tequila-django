Tequila-django
==============

Changes

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
            version: v0.9.0
            name: tequila-django

          - src: geerlingguy.nodejs
            version: 4.1.1
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
          nodejs_version: "7.x"
          nodejs_install_npm_user: "{{ project_name }}"
          nodejs_package_json_path: "{{ source_dir }}"

        If you previously had anything configured under the variable
        ``global_npm_installs``, rename this variable to
        ``nodejs_npm_global_packages``.

     #. Modify your deployment/playbooks/web.yml file (or equivalent)
        to include the nodejs role _after_ the tequila-django role::

          ---
          - hosts: web
            become: yes
            roles:
              - tequila-nginx
              - { role: tequila-django, is_web: true }
              - nodejs
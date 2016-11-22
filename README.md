Ansible Nginx Role
=========

This role will provision an nginx server.

Requirements
------------

No pre-requisite packages are required

Role Variables
--------------

http_port - Port nginx will listen on for http connections [default:80]
https_port - Port nginx will listen on for https connections [default:443]

Dependencies
------------

No other roles required

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      become: true
     roles:
       - nginx

License
-------

BSD

Author Information
------------------

Rich Nason - http://nason.co

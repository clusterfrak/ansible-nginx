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
status_port - Port nginx will use to serve webserver stats [default:8080]
site_name - Site that nginx will set up a default server instance for. [default:"nginx.local"]

System Variables
------------

 SITE_NAME - If the SITE_NAME variable has been exported on the server prior to the playbook being ran, nginx will setup a default web directory and config file in /etc/nginx/conf.d/{{SITE_NAME}} configured with the {{SITE_NAME}} value. 

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

 This playbook will set up nginx, and create a /var/www/html/nginx.local directory along with an nginx config file located at /etc/nginx/conf.d/nginx.local.conf. The config file will be set up and configured to listen for nginx.local. Logs and path location will also be set using nginx.local. 

 Example 2 Playbook
----------------

`export SITE_NAME="mysite.com"`

	- hosts: servers
	      become: true
	     roles:
	       - nginx

 This playbook will set up nginx, and create a /var/www/html/mysite.com directory along with an nginx config file located at /etc/nginx/conf.d/mysite.com.conf. The config file will be set up and configured to listen for mysite.com. Logs and path location will also be set using mysite.com. 

 Site Reconfigure
 ----------------

 In the event that the playbook was ran using the default values (nginx.local), and you would like to replace the directories and config with mysite.com, simply export the environment variable and run the nginx-reconf.yml playbook located in the role's file directory

 ```bash
 export SITE_NAME="mysite.com"
 cd /etc/ansible/roles/clusterfrak.nginx/files
 ansible-playbook nginx-reconfig.yml
 ```

 This playbook will rename the nginx.local directories to mysite.com and reconfigure the nginx config file to set the server, logs, and paths to mysite.com


License
-------

BSD

Author Information
------------------

Rich Nason - http://nason.co
http://clusterfrak.com
http://appcontainers.com

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
site_name - Site that nginx will set up a default server instance for [default:"nginx.local"]
nginx_ssl_dir - Location that nginx will store any generated certificates [default:"/etc/nginx/ssl"]

System Variables
------------

 SITE_NAME - If the SITE_NAME variable has been exported on the server prior to the playbook being ran, nginx will setup a default web directory and config file in /etc/nginx/conf.d/{{SITE_NAME}} configured with the {{SITE_NAME}} value.

 If SITE_NAME was not exported prior to the server being provisioned, the variable can be exported and the server can be reconfigured using the __nginx-reconfig.yml__ playbook located in /etc/ansible/roles/clusterfrak.nginx/files/nginx-reconfig.yml

 SITE_NAME is also used to automatically setup up new sites that nginx will listen for. To set up a new site, simply export SITE_NAME="somedomain.com" and then run the /etc/ansible/roles/clusterfrak.nginx/files/nginx-newsite.yml playbook. This will automatically create a /var/www/html/somedomain.com directory, as well as a /etc/nginx/conf.d/somedomain.com.conf config file that will listen for somedomain.com. It will also provision self signed certificates for somedomain.com and put them in /etc/nginx/ssl. The config file will automatically be configured to use these generated certificates.

Dependencies
------------

No other roles required

Example Playbook
----------------

This playbook will set up nginx, and create a /var/www/html/nginx.local directory along with an nginx config file located at /etc/nginx/conf.d/nginx.local.conf. The config file will be set up and configured to listen for nginx.local. Logs and path location will also be set using nginx.local:

    - hosts: servers
      become: true
     roles:
       - nginx 

 Example 2 Playbook
----------------

This playbook will set up nginx, and create a /var/www/html/mysite.com directory along with an nginx config file located at /etc/nginx/conf.d/mysite.com.conf. The config file will be set up and configured to listen for mysite.com. Logs and path location will also be set using mysite.com. 

`export SITE_NAME="mysite.com"`

	- hosts: servers
	      become: true
	     roles:
	       - nginx

 Site Reconfigure
 ----------------

 In the event that the playbook was ran using the default values (nginx.local), and you would like to replace the directories and config with mysite.com, simply export the environment variable and run the nginx-reconf.yml playbook located in the role's file directory

 ```bash
 export SITE_NAME="mysite.com"
 cd /etc/ansible/roles/clusterfrak.nginx/files
 ansible-playbook nginx-reconfig.yml
 ```

 Running the /etc/ansible/roles/clusterfrak.nginx/files/nginx-reconfig.yml playbook will rename the nginx.local directories to mysite.com and reconfigure the nginx config file to set the server, logs, and paths to mysite.com. It will also generate a certificate in /etc/nginx/ssl and configure the mysite.com.conf to use the newly generated self signed certificate.

 New Site Configuration
 ----------------

 To set up a new site, simply export SITE_NAME="somedomain.com" and then run the /etc/ansible/roles/clusterfrak.nginx/files/nginx-newsite.yml playbook. This will automatically create a /var/www/html/somedomain.com directory, as well as a /etc/nginx/conf.d/somedomain.com.conf config file that will listen for somedomain.com. It will also provision self signed certificates for somedomain.com and put them in /etc/nginx/ssl. The config file will automatically be configured to use these generated certificates.

 ```bash
 export SITE_NAME="mysite.com"
 cd /etc/ansible/roles/clusterfrak.nginx/files
 ansible-playbook nginx-newsite.yml
 ```

License
-------

BSD

Author Information
------------------

Rich Nason - http://nason.co
http://clusterfrak.com
http://appcontainers.com

# Ansible Nginx Role
-------

This is an Ansible role that will provision a fresh Nginx web server installation on a variety of platforms and provide a few tools to get users started quickly and easily spinning up new sites within the web server. The role utilizes a few variables that will allow the role to install with set customizations such as the http/https ports that Nginx will listen on, the directory location that SSL certificates will be stored, and an automatic basic Nginx configuration generator, that will create a new site directory, config file for the new site, as well as auto generate a self signed certificate that Nginx can use to serve the new site once there has been content placed into the newly created web directory. 

<br>

## More Documentation
-------
[clusterfrak.com](http://clusterfrak.com/devops/ansible/ansible_nginx/)

<br>

## Requirements
-------

__1. &nbsp;&nbsp; Install dependencies:__ <br>

> RedHat based distros (RHEL, CentOS, AL):

```bash
sudo yum -y install epel-release
sudo yum clean all
sudo yum -y install ansible
```
<br><br>

> Debian based distros (Debian, Ubuntu):

```bash
sudo apt-get update
sudo apt-get install -y curl vim python python-dev python-openssl libffi-dev libssl-dev gcc
curl "https://bootstrap.pypa.io/get-pip.py" -o "/tmp/get-pip.py"
python /tmp/get-pip.py
pip install pip ansible --upgrade
rm -fr /tmp/get-pip.py
```

<br>

__3. &nbsp;&nbsp; Create directory structure:__ <br>

Create the directory structure that you are going to use. In this tutorial we are going to set up ansible roles in __/etc/ansible/roles__

<br>

```bash
mkdir -p /etc/ansible/roles || exit 0
```
<br>

__4. &nbsp;&nbsp; Set ansible host:__

Set Ansible localhost entry so that ansible knows it will run against localhost and can talk to itself on localhost without attempting to open a TCP socket connection. 

<br>

```bash
echo localhost ansible_connection=local > /etc/ansible/hosts
```

<br>

## Role Variables
-------

The clusterfrak.nginx role uses a few environment variables to automatically configure Nginx. The role is set with default values for each of the available variables. Ansible will attempt to gather shell environment variable values and use those values to over-ride the default values that are set. If no shell environment variable is available or set, then ansible will configure itself to use the default values. In order to customize the installation of Nginx, simply export the ansible corresponding shell variable to set the value to something other than default prior to installing the role.

<br>

> Ansible Variables:

 - http_port: 80
 - https_port: 443
 - status_port: 8080
 - site_name: "nginx.local"
 - nginx_ssl_dir: "/etc/nginx/ssl"

<br>

> Mapped Shell Environment Variables:

 - HTTP_PORT - Port nginx will listen on for http connections [default:80]
 - HTTPS_PORT - Port nginx will listen on for https connections [default:443]
 - STATUS_PORT - Port nginx will use to serve web server stats [default:8080]
 - SITE_NAME - Site that nginx will set up a default server instance for [default:"nginx.local"]
 - SSL_DIR - Location that nginx will store any generated certificates [default:"/etc/nginx/ssl"]

<br>

 > Setting Shell Environment Variables:

 To set a variable value simply export the variable prior to running the role install playbook.

<br>

```bash
export SITE_NAME="mydomain.com"
```

 <br>

## Dependencies
-------

No other roles required in order to run this role

<br>

## Example Playbook With Default Values
-------

This playbook will set up nginx, and create a /var/www/html/nginx.local directory along with an nginx config file located at /etc/nginx/conf.d/nginx.local.conf. The config file will be set up and configured to listen for nginx.local. Logs and path locations will also be set using nginx.local:

    - hosts: servers
      become: true
     roles:
       - nginx 

## Example Playbook With Custom Values
-------

This playbook will set up nginx, and create a /var/www/html/mysite.com directory along with an nginx config file located at /etc/nginx/conf.d/mysite.com.conf. The config file will be set up and configured to listen for mysite.com. Logs and path location will also be set using mysite.com. 

`export SITE_NAME="mysite.com"`

	- hosts: servers
	      become: true
	     roles:
	       - nginx

## Nginx Role Support Playbooks:
-------

__/etc/ansible/roles/clusterfrak.nginx/files/nginx-reconfig.yml:__ <br>

The nginx-reconfig.yml file is a playbook that will reconfigure a nginx install that has occurred using this role, with the default values set. If the role was installed with the default values, and you would like to reconfigure the server to instead use a custom site, then this playbook will do exactly that. 

Installing this role with the default values will do the following:

 - Create a __/var/www/html/nginx.local__ directory
 - Create a __/etc/nginx/conf.d/nginx.local.conf__ nginx config file
 - All log paths, log locations, etc will reference nginx.local in the nginx config

In order to either create a self signed certificate for nginx.local or reconfigure the server to use a different domain besides nginx.local, then use the following to execute this playbook.

<br>

```bash
cd /etc/ansible/roles/clusterfrak.nginx/files
ansible-playbook nginx-reconfig.yml
```

<br>

> ### EXAMPLE: 

__For this example we will assume we have already ran the following `export SITE_NAME=mydomain.com` on your nginx host__

<br>

This playbook will perform the following tasks:

 - If `SITE_NAME` has not been set, or is still set to nginx.local, then the reconfig playbook will simply generate a self signed certificate for the nginx.local domain and restart the nginx process, skipping all other major steps in the playbook.
 - If a certificate already exists for nginx.local, it will detect the existing cert and skip the cert generation step as well. 
 - If `SITE_NAME` has been exported to a value other than nginx.local, then the playbook will use the value of `SITE_NAME`
 - The __/var/www/html/nginx.local__ directory will be renamed to __/var/www/html/mydomain.com__
 - The __/etc/nginx/conf.d/nginx.local.conf__ will be moved to __/etc/nginx/conf.d/mydomain.com.conf__
 - All paths, log names and references of nginx.local with be replaced with mydomain.com mydomain.com.conf file 
 - A new self signed certificate key pair will be generated for mydomain.com and placed in __/etc/nginx/ssl__

<br>

__/etc/ansible/roles/clusterfrak.nginx/files/nginx-newsite.yml:__

<br>

> ### PRE-REQUISITE STEP: 

__Prior to running the following nginx-newsite.yml playbook, remember to re-export `SITE_NAME` to the new site that you wish to configure!__

<br>

> ### EXAMPLE:

__For this example we will assume we have already ran the following `export SITE_NAME="mycoolsite2.com"` on your nginx host__

<br>

In the event that you have installed the nginx role, configured for a site, and decided, that you want to create a new site on the same nginx server, a playbook to create a template for a new site has also been automatically included in the role. Again going to __/etc/ansible/roles/clusterfrak.nginx/files/__ and running the `ansible-playbook nginx-newsite.yml` playbook will perform the following tasks:

<br>

 - Create the directory __/var/www/html/mycoolsite2.com__
 - Create a new nginx config in __/etc/nginx/conf.d/mycoolsite2.com.conf__
 - Configure the config file with all paths, log locations, and references in __/etc/nginx/conf.d/mycoolsite2.com.conf__ to mycoolsite2.com
 - Create a new self signed certificate pair in __/etc/nginx/ssl/mycoolsite2.com.key__ and __/etc/nginx/ssl/mycoolsite2.com.crt__

<br>

License
-------

BSD

Author Information
-------

[Rich Nason](http://nason.co) <br>
[Clusterfrak Doc Site](http://clusterfrak.com) <br>
[Container Doc Site](http://appcontainers.com) <br>

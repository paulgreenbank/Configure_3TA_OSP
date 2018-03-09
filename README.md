# Ansible Playbook: 3Tier Application

Installs RedHat Automation specialist 3Tier Application assignment.

## Requirements

No special requirements are need for playbook and roles.

## Role Variables 

Available variables are listed below, along with set values (see `<role>/vars/main.yml`):

    haproxy_packages:
	  - httpie
	  - haproxy

The `haproxy` package and dependencies installed. By default this role installs HA Proxy and will ensure all packages are at their latest available version on each run. 

    haproxy_config_template: haproxy.cfg.j2

The HA Proxy template file. This will be used during setup to configure HA Proxy to listen to the `apps` backend Tomcat instances.

    haproxy_config: /etc/haproxy/haproxy.cfg

The location of the HA Proxy configuration file on the `frontend` servers.

    postgres_package: postgresql-server

The Postgres package name to be insatlled, this is set latest and will ensure its at its latest available version on each run.

    tomcat_webapp_owner: "tony.g.kay<at>gmail.com"

Default web application contact person, this is populated into the Tomcat application template and displayed on the application web page.

    tomcat_package: tomcat

The `tomcat` package name installed. By default this role installs Tomcat and ensures the packages is at its latest available version on each run. 

    tomcat_webapps_directories:
	  - /usr/share/tomcat/webapps/ROOT
	  - /usr/share/tomcat/webapps/ansible

These are the application install directories where the tomcat application pages are deployed.
 
## Dependencies

  - None

## Example Playbook

The required playbook has been created to call the roles to install the various parts of the 3 Tier Application, this is called post environment setup (either OpenStack or AWS),
it's included here for reference:

	---
	## Configure and deploy 3Tier Application

	## RedHat Ansible Boot Camp homework assignment
	## Playbook have been written as a PoC for a 3Tier Application deployment
	## All playbook have been written via Ansible Core and used in Ansible Tower

	## Created a written by Paul Greenbank - Technical Consultant for Open System Specialists

	## Playbook description:
	#
	# Playbook uses 3 configuration roles and a tagged in memory inventory role
	# to configure the OpenStack instances.
	# Inventory groups are created based on OpenStack meta tags used during instance deployment.
	# The openstack_hosts role has been tagged so when deploying application into AWS
	# this role can be excluded from the play in the Ansible Tower template using skip tags.
	# The yum repo file is encrypted using ansible vault to protect it's contents while stored in
	# public git repository.

	## Playbook enhancements post PoC:
	#
	# Enhancements would be to remove the in memory inventory in favour of of setting up
	# a inventory using the OpenStack source
	#

	- hosts: jumpbox
	  gather_facts: false
	  roles:
		- { role: openstack_hosts, tags: openstack }

	- name: Add yum repo for 3Tier application
	  hosts: appsdb:apps:frontends
	  gather_facts: true
	  become: yes
	  tasks:
		- name: Including common role
		  include_role:
			name: 3tier_common

	- name: Install Postgres for 3Tier application
	  hosts: appsdb
	  gather_facts: true
	  become: yes
	  tasks:
		- name: Including postgres role
		  include_role:
			name: 3tier_postgres

	- name: Install Tomcat for 3Tier application
	  hosts: apps
	  gather_facts: true
	  become: yes
	  tasks:
		- name: Including tomcat role
		  include_role:
			name: 3tier_tomcat

	- name: Install HAProxy for 3Tier application
	  hosts: frontends
	  gather_facts: true
	  become: yes
	  tasks:
		- name: Including haproxy role
		  include_role:
			name: 3tier_haproxy


## License

MIT / BSD

## Author Information

This role was created in 2018 by Paul Greenbank - Technical Consultant (https://www.oss.co.nz/).

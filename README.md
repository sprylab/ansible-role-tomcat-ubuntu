Ansible role: Tomcat Ubuntu
===========================

Installs the very latest Apache Tomcat (currently 8.0.30) on Ubuntu 14.04 LTS and up by downloading the official .deb packages directly from [packages.ubuntu.com][1].

Additionally, this role provides support for easily:

* setting _JAVA_OPTS_ for Tomcat
* installing the Tomcat Native Library
* installing MySQL and/or PostreSQL JBDC drivers
* defining _GlobalNamingResources_
* defining _jvmRoute_

Installation
------------

You can easily install this role using [Ansible Galaxy][2]:

    $ ansible-galaxy install sprylab.tomcat-ubuntu

Requirements
------------

A Java Runtime is required for Tomcat to run. This role does not put a hard dependency on any JRE, though.

You have to make sure Java 7 or up is installed before executing this role on a target. It's known to work perfectly with [smola.java][3], but it's up to you to decide.

Role Variables
--------------

* ``tomcat_ubuntu_version``: Tomcat version to install (string, default: ``8.0.30-1_all``)
* ``tomcat_ubuntu_java_opts``: _JAVA_OPTS_ used for starting Tomcat (string, default: ``-Djava.awt.headless=true -Xmx2048m -XX:+UseConcMarkSweepGC``)
* ``tomcat_ubuntu_global_naming_resources``: complete ``Resource`` XML tag(s) used as _GlobalNamingResource(s)_ (string, default: empty)
* ``tomcat_ubuntu_jvm_route``: _jvmRoute_ to assign to this Tomcat installation, needed for proper load balancing configuration (use ``"{{ ansible_hostname }}"`` for the machine's hostname') (string, default: empty)
* ``tomcat_ubuntu_download_dir``: Directory to store the downloaded .deb packages (string, default: ``/tmp/tomcat8``)
* ``tomcat_ubuntu_native_install``: Enables or disables the installation of the Tomcat Native Library; recommended for production environments (bool, default: ``yes``)
* ``tomcat_ubuntu_native_version``: Tomcat Native Library version to install (string, default: ``1.1.33-1_amd64``)
* ``tomcat_ubuntu_apache_native_version``: Apache Portable Runtime version to install (string, default: ``1.5.2-3_amd64``)
* ``tomcat_ubuntu_mysql_jbdc_install``: Enables or disables the installation of the MySQL JBDC driver (bool, default: ``no``)
* ``tomcat_ubuntu_postgresql_jbdc_install``: Enables or disables the installation of the PostgreSQL JBDC driver (bool, default: ``no``)
* ``tomcat_ubuntu_postgresql_jdbc_version``: PostgreSQL JBDC driver version to install (string, default: ``9.4.1207``)

Please note: ``tomcat_ubuntu_version``, ``tomcat_ubuntu_native_version`` and ``tomcat_ubuntu_apache_native_version`` must
be set in .deb package version format incl. architecture. These versions *must* exist in the Ubuntu Package archive.

Dependencies
------------

No hard dependency is set, but please note the _Requirements_ section above.

Example Playbook
----------------

Just use this role in your playbook as usual:

    - hosts: web
      roles:
         - sprylab.tomcat-ubuntu

You may easily override the default variables, e.g.:

    - hosts: web
      roles:
         - { role: sprylab.tomcat-ubuntu, tomcat_ubuntu_postgresql_jbdc_install: yes }


The ```examples``` directory also gives you an impression how to use this role. It contains a ``Vagrantfile``,
which quickly starts up an virtual Ubuntu 14.04 LTS box and provisions it playing the ``site.yml`` playbook.

To try it out follow these steps:

* Install Vagrant and VirtualBox
* Open a terminal and change to the ``examples`` directory
* Add Ubuntu 14.04 LTS box:
    ``vagrant box add ubuntu/trusty64``
* Initialize the VM:
    ``vagrant up``
 * When finished, check that Tomcat is running by looking up ``http://localhost:8080/`` in your Browser
 (Vagrant is configured to forward the port 8080 from the VM to your host).
 * Connect to the VM via SSH to check further details:
    ``vagrant ssh``

License
-------

    Copyright 2016 sprylab technologies GmbH

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.

Author Information
------------------

By Roman Zimmer (sprylab technologies GmbH)

  [1]: http://packages.ubuntu.com/search?keywords=tomcat8&searchon=names&suite=xenial&section=all
  [2]: https://galaxy.ansible.com/sprylab/tomcat-ubuntu/
  [3]: https://galaxy.ansible.com/smola/java/
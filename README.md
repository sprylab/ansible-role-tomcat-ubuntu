Ansible role: Tomcat Ubuntu
===========================

Installs the very latest Apache Tomcat (currently 8.0.36) on Ubuntu 14.04 LTS and up by downloading the official .deb packages directly from [packages.ubuntu.com][1].

Additionally, this role provides support for easily:

* setting _JAVA_OPTS_ and _CATALINA_OPTS_ for Tomcat
* installing Tomcat admin web applications and tools to create user instances
* installing the Tomcat Native Library
* installing MySQL and/or PostreSQL JBDC drivers
* installing additional Tomcat user instances
* defining _GlobalNamingResources_
* defining _jvmRoute_
* defining _tomcat-users.xml_
* defining _HTTP_ port for listening and controlling

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

* `tomcat_ubuntu_version`: Tomcat version to install (string, default: `8.0.36-2ubuntu1_all`)
* `tomcat_ubuntu_repository`: URL (without trailing slash) pointing to Ubuntu repository to install from (string, default: `http://de.archive.ubuntu.com/ubuntu`)
* `tomcat_ubuntu_port`: Port for Tomcat's HTTP connector (integer, default: `8080`)
* `tomcat_ubuntu_control_port`: Port for controlling Tomcat via HTTP, e.g. _shutdown_ (integer, default: `8005`)
* `tomcat_ubuntu_java_opts`: _JAVA_OPTS_ used for starting Tomcat (string, default: `-Djava.awt.headless=true -Xmx2048m -XX:+UseConcMarkSweepGC`)
* `tomcat_ubuntu_catalina_opts`: _CATALINA_OPTS_ used for starting Tomcat (string, default: empty)
* `tomcat_ubuntu_users`: List of users defined in Tomcat's `tomcat-users.xml` (list of `{ name: <name>, password: <password>, roles: <comma-separated roles> }` elements, default: [])
* `tomcat_ubuntu_global_naming_resources`: Complete `Resource` XML tag(s) used as _GlobalNamingResource(s)_ (string, default: empty)
* `tomcat_ubuntu_jvm_route`: _jvmRoute_ to assign to this Tomcat installation, needed for proper load balancing configuration (use `"{{ ansible_hostname }}"` for the machine's hostname') (string, default: empty)
* `tomcat_ubuntu_download_dir`: Directory to store the downloaded .deb packages (string, default: `/tmp/tomcat8`)
* `tomcat_ubuntu_admin_install`: Enables or disables the installation of the Tomcat admin web applications (bool, default: `no`)
* `tomcat_ubuntu_user_install`: Enables or disables the installation of the Tomcat tools to create user instances (bool, default: `no`)
* `tomcat_ubuntu_native_install`: Enables or disables the installation of the Tomcat Native Library; recommended for production environments (bool, default: `yes`)
* `tomcat_ubuntu_native_version`: Tomcat Native Library version to install (string, default: `1.2.8-1_amd64`)
* `tomcat_ubuntu_apache_native_version`: Apache Portable Runtime version to install (string, default: `1.5.2-4_amd64`)
* `tomcat_ubuntu_libecj_version`: Eclipse Java compiler version to install; needed by `libtomcat8-java` package (string, default: `3.11.0-1ubuntu2_all`)
* `tomcat_ubuntu_libssl_version`: Secure Sockets Layer toolkit version to install; needed by `libtcnative` package, i.e. when `tomcat_ubuntu_native_install` is enabled (string, default: `1.0.2g-1ubuntu5_amd64`)
* `tomcat_ubuntu_mysql_jbdc_install`: Enables or disables the installation of the MySQL JBDC driver (bool, default: `no`)
* `tomcat_ubuntu_postgresql_jbdc_install`: Enables or disables the installation of the PostgreSQL JBDC driver (bool, default: `no`)
* `tomcat_ubuntu_postgresql_jdbc_version`: PostgreSQL JBDC driver version to install (string, default: `9.4.1208`)
* `tomcat_ubuntu_user_instances`: Describes additional Tomcat user instances to set up, which share the same files as the main instance (list of object, default: `[]`)
  * For each additional Tomcat user instance a object with the following structure must be created:
    * `name`: Name of the user instance - will be used as directory name (string)
    * `port`: Port the user instance should listen on (integer)
    * `control_port`: Port for controlling the user instance (integer)
    * `parent_directory`: Parent directory, where the user instance should be created (string)
    * `java_opts`: _JAVA_OPTS_ the user instance passes to the JVM (list of string)
    * `catalina_opts`: _CATALINA_OPTS_ the user instance should use (list of string)
    * `global_naming_resources`: Complete `Resource` XML tag(s) used as _GlobalNamingResource(s)_ for the user instance (string)
    * `auto_start`: Enables or disables starting the user instance after set up and on boot (bool)
    * `admin_install`: Enables or disables the installation of the Tomcat admin web applications for the user instance (bool)
  * The Tomcat user instance will be created in `{{ user_instance.parent_directory }}/{{ user_instance.name }}`.
  * For every Tomcat user instance a init script is generated in `/etc/init.d/{{ user_instance.name }}` just like for the main instance.

Please note: `tomcat_ubuntu_version`, `tomcat_ubuntu_native_version` and `tomcat_ubuntu_apache_native_version` must
be set in .deb package version format incl. architecture. These versions *must* exist in the Ubuntu Package archive.

Tags
----

* `jvm-options`: Just run all commands necessary to set the _JAVA_OPTS_ and _CATALINA_OPTS_ (Tomcat main instance only)
* `user-instances`: Just run all commands necessary to set up all Tomcat user instances

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


The `examples` directory also gives you an impression how to use this role. It contains a `Vagrantfile`,
which quickly starts up an virtual Ubuntu 14.04 LTS box and provisions it playing the `site.yml` playbook.

To try it out follow these steps:

* Install Vagrant and VirtualBox
* Open a terminal and change to the `examples` directory
* Add Ubuntu 14.04 LTS box:
    `vagrant box add ubuntu/trusty64`
* Initialize the VM:
    `vagrant up`
 * When finished, check that Tomcat is running by looking up `http://localhost:8080/` in your web browser
 (Vagrant is configured to forward the port 8080 from the VM to your host).
 * Connect to the VM via SSH to check further details:
    `vagrant ssh`

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

By Roman Zimmer ([sprylab technologies GmbH][4])

  [1]: http://packages.ubuntu.com/search?keywords=tomcat8&searchon=names&suite=xenial&section=all
  [2]: https://galaxy.ansible.com/sprylab/tomcat-ubuntu/
  [3]: https://galaxy.ansible.com/smola/java/
  [4]: https://sprylab.com/

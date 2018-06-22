stone-payments.mongodb
============
[![Build Status](https://travis-ci.org/stone-payments/ansible-mongodb.svg?branch=feat%2Fmolecule)](https://travis-ci.org/stone-payments/ansible-mongodb)

Role for Ansible which manages MongoDB in a standalone setup or replica set.

## Supported systems
To conserve development efforts, we decided that a supported distro should:

* be currently supported by the distro-maker (aka not in EOL);
* be currently supported by MongoDB.org (this requirement will probably be dropped soon);
* be systemd-based;
* have a wide-enough user-base.

Therefore, the supported systems list is currently:

* Enterprise Linux (both CentOS and RHEL)
  * 7.3
  * 7.4
  * 7.5
* Ubuntu
  * 16.04

Further distros may be added upon request, as long as the requirements are met.

## Usage
### Quickstart
There's absolute no variable needed to setup a basic, passwordless, loopback-only, standalone MongoDB setup. Just
include it in a play:
```yaml
- name: install mongodb
  hosts: all
  roles: stone-payments.mongodb
```

### Replica set setup
In order to build a replica set, you need to inform the master that he is a master, and a replica on which master to
connect to. You can do all this with the following excerpt:
```yaml
- name: install mongodb replica set
  host: all
  roles: stone-payments.mongodb
  vars:
    mongodb_conf_bindIp: "0.0.0.0"
    mongodb_replSet_enabled: true
    mongodb_replSet_name: "someReplicaSetName"
    mongodb_replSet_master: "1.2.3.4" #must be an IP address
    mongodb_replSet_key: "someLongKey" #optional, cross-replica authentication key
    mongodb_replSet_member: "{{ ansible_eth1['ipv4']['address'] }}" #optional, specify a different IF for replication
```

### Authentication
You can enable authentication and create an admin account the following way:
```yaml
- name: install mongodb with authentication
  hosts: all
  roles: stone-payments.mongodb
  vars:
    mongodb_conf_auth: true
    mongodb_admin_user: "admin"
    mongodb_admin_password: "somePassword"
```

### Logging
You can set any [systemLog](https://docs.mongodb.com/manual/reference/configuration-options/#systemlog-options)
option by providing `mongodb_conf_logging` dictionary:
```yaml
- name: install mongodb with network debug logging
  host: all
  roles: stone-payments.mongodb
  vars:
    mongodb_conf_logging:
      verbosity: 0
      component:
        network:
          verbosity: 5
      destination: file
      path: /var/log/mongodb/mongod.log
```

### Firewall
This rule will configure either ufw or firewalld to enable incoming connections by default. You may customize this with
the following options (which are specific to the firewall solution you're utilizing):
```yaml
- name: install mongodb with custom firewall settings
  hosts: all
  roles: stone-payments.mongodb
  vars:
    mongodb_firewall_zone: "public" #firewalld only
    mongodb_firewall_interface: "eth0" #ufw only
    mongodb_firewall_source: "192.168.0.0/24" #ufw only
```
You may also suppress firewall config by setting `mongodb_install_firewall: false`.

### Other configs
I believe almost every other config is self-explanatory or directly related to a MongoDB core feature. Simply override
the configs on `defaults/main.yml` and they will be (hopefully) applied to your system.

## Testing
This role implements unit tests with [Molecule](https://molecule.readthedocs.io/) on Docker. Notice that we only
support Molecule 2.0 or greater. You can install Molecule and the Docker interaction library inside a virtual
environment with the following commands. Notice that we need docker-py both inside and outside the virtualenv.
```sh
sudo pip install docker-py
virtualenv .venv
.venv/bin/activate
pip install molecule docker-py
```
The Docker installation and configuration is out of scope.

If you have a SELinux-enabled host, you must also have the libselinux-python library installed. There's a special
addition in the Molecule playbook when delegating tasks to localhost to use the host's python interpreter instead of
the virtualenv python in order to properly access the SELinux bindings. You can install this package both on Fedora and
CentOS with:
```sh
sudo yum install python2-libselinux
```

After having Molecule setup within the virtualenv, you can run the tests with:
```sh
molecule converge [-s scenario_name]
```
Where `scenario_name` is the name of a test case under `molecule`. The default test case is run if no parameter is
passed.

## Contributing
Just open a PR. We love PRs!

### To Do List
Here's some suggestions on what to do:

* Support use of distro-packaged MongoDB.
* Write further standalone tests with serverspec or testinfra.
* Improve the test case for the replica set.

## License
This role is distributed under the MIT license.

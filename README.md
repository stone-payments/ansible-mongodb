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
  * 7.2
  * 7.3
  * 7.4
* Ubuntu
  * 16.04

Further distros may be added upon request, as long as the requirements are met.

## Usage
### Quickstart
There's absolute no variable needed to setup a basic, passwordless, loopback-only, standalone MongoDB setup. Just
include it in a play:
```
- name: install mongodb
  hosts: all
  roles: stone-payments.mongodb
```

### Replica set setup
In order to build a replica set, you need to inform the master that he is a master, and a replica on which master to
connect to. You can do all this with the following excerpt:
```
- name: install mongodb replica set
  host: all
  roles: stone-payments.mongodb
  vars:
    mongodb_conf_bindIp: "0.0.0.0"
    mongodb_replSet_name: "someReplicaSetName"
    mongodb_replSet_master: "1.2.3.4"
    mongodb_replSet_isMaster: "{{ true if mongodb_replSet_master in ansible_all_ipv4_addresses else false }}"
```

### Other configs
I believe almost every other config is self-explanatory or directly related to a MongoDB core feature. Simply override
the configs on `defaults/main.yml` and they will be (hopefully) applied to your system.

## Testing
This role implements unit tests with [Molecule](https://molecule.readthedocs.io/) on Docker. Notice that we only
support Molecule 2.0 or greater. You can install molecule with:
```bash
pip install molecule
```
The Docker installation and configuration is out of scope.

After having Molecule setup, you can run the tests with:
```bash
molecule converge
```

## Contributing
Just open a PR. We love PRs!

### To Do List
Here's some suggestions on what to do:

* Support use of distro-packaged MongoDB.
* Support Ubuntu Zesty and Artful.
* Write further standalone tests with serverspec or testinfra.
* Write a test case for the replica set.

## License
This role is distributed under the MIT license.

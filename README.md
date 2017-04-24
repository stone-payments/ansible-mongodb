stone-payments.mongodb
============
Role for Ansible which manages MongoDB in a standalone setup or replica set

# Quickstart
There's absolute no variable needed to setup a basic, passwordless,
loopback-only, standalone MongoDB setup. Just include it in a play:
```
- name: install mongodb
  hosts: all
  roles: buy4.mongodb
```

# Replica set setup
In order to build a replica set, you need to inform the master that he is a
master, and a replica on which master to connect to. You can do all this with
the following excerpt:
```
- name: install mongodb replica set
  host: all
  roles: buy4.mongodb
  vars:
    mongodb_conf_bindIp: "0.0.0.0"
    mongodb_replSet_name: "someReplicaSetName"
    mongodb_replSet_master: "1.2.3.4"
    mongodb_replSet_isMaster: "{{ true if mongodb_replSet_master in ansible_all_ipv4_addresses else false }}"
```

# Other configs
I believe almost every other config is self-explanatory or directly related to
a MongoDB core feature. Simply override the configs on `defaults/main.yml` and
they will be (hopefully) applied to your system.

# Included ansible module
There's a Python Ansible module included in the `library` folder. Altough it
needs some refactoring, it is able to join and remove nodes from a mongodb
replica set in an idempotent way.

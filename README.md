Role Name
=========

An Ansible role for bootstrapping a consul cluster on docker hosts.

Requirements
------------

A respectably recent version of Docker should be already installed on the target machine. 

Role Variables
--------------

```yml
consul_hostname: server-1.my.company.lan
consul_command: -server -advertise {{ ansible_default_ipv4['address'] }} -bootstrap-expect 3
```
The `consul_command` can be any valid consul command. You might want to bootstrap the primary server
with this command:
```
consul_command: "-server -advertise {{ ansible_default_ipv4['address'] }} -bootstrap-expect 3"
```
and then start the secondary servers with this command:
```
consul_command: "-server -advertise {{ ansible_default_ipv4['address'] }} -join {{ hostvars[groups['docker_cluster_primary'][0]]['ansible_default_ipv4']['address'] }}"
```
where the join IP is the IP of the first consul instance.

Dependencies
------------

This role requires the `emmetog.docker-compose` role since docker-compose is used to start the consul services.

Usage
-----

First install the role from ansible galaxy:
```
$ ansible-galaxy install emmetog.consul
```

Then use the role in a playbook as follows:
```yml
- hosts: server_primary
  roles:
     - {
        role: emmetog.consul,
        consul_command: "-server -advertise {{ ansible_default_ipv4['address'] }} -bootstrap-expect 3"
     }
- hosts: server_secondaries
  roles:
     - {
        role: emmetog.consul,
        consul_command: "-server -advertise {{ ansible_default_ipv4['address'] }} -join {{ hostvars[groups['server_primary'][0]]['ansible_default_ipv4']['address'] }}"
     }
```

In reality you can use any consul command you want to, the example above will get you started with a simple consul cluster.

For example, you could alternatively set up a single consul instance using this:
```yml
- hosts: server_primary
  roles:
     - {
        role: emmetog.consul,
        consul_command: "-server -advertise {{ ansible_default_ipv4['address'] }} -bootstrap"
     }
```

License
-------

BSD

Author Information
------------------

Made with love by Emmet O'Grady.

I am the founder of [NimbleCI](https://nimbleci.com) which builds Docker containers for feature branch workflow projects in Github.

I blog on my [personal blog](http://blog.emmetogrady.com) and about Docker related things on the [NimbleCI blog](http://blog.nimbleci.com).


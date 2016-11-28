# Accessing a VM just created

After standard creation of a VM, it only contains a private IP addresse and therefore can not be accessed from any network outside OpenStack.

~~~
$ openstack server list
+--------------------------------------+------+--------+------------------+---------------------+
| ID                                   | Name | Status | Networks         | Image Name          |
+--------------------------------------+------+--------+------------------+---------------------+
| 90a7997e-e0a7-4f94-9c31-307c3d643170 | myvm | ACTIVE | paasnet=10.0.0.6 | Ubuntu-14.04-x86_64 |
+--------------------------------------+------+--------+------------------+---------------------+
~~~

In order to access a vm from outside, a floating IP has to be allocated to the VM.


NB: for the following steps, it is important to be authenticated with a user that belongs to the tenant network. Make sure the project name is correctly set. For example, this list is using the 'paas' project:
~~~
OS_PROJECT_NAME=paas
~~~

Otherwise, if the an adminsistrator is authenticated it is possible to pass the desired project on each command using the '--tenant-id' argument.

## Creating a floating ip

To create a floating IP, it is necessary to pass the external network. To find it, list the networks created:

~~~
$ neutron net-list
+--------------------------------------+---------+--------------------------------------------------+
| id                                   | name    | subnets                                          |
+--------------------------------------+---------+--------------------------------------------------+
| 8ebd252c-5488-4600-aba3-2f7b4fcdff48 | paasnet | 4d6f2b18-6643-41b2-a71a-c98e204d2b5f 10.0.0.0/24 |
| a985b606-dacb-4561-baf6-d29fb9eb20bb | ext-net | ef974aeb-4795-427f-9481-2cfdcfa86dc4             |
+--------------------------------------+---------+--------------------------------------------------+
~~~

In this case, it the 'ext-net' work.


To create the floating IP, execute:

~~~
$ neutron floatingip-create ext-net
Created a new floatingip:
+---------------------+--------------------------------------+
| Field               | Value                                |
+---------------------+--------------------------------------+
| fixed_ip_address    |                                      |
| floating_ip_address | 172.16.42.3                          |
| floating_network_id | a985b606-dacb-4561-baf6-d29fb9eb20bb |
| id                  | 9ae7601a-5324-4830-81ee-194ed8a4d313 |
| port_id             |                                      |
| router_id           |                                      |
| status              | DOWN                                 |
| tenant_id           | 2d423f42d64243e1b6aebb223d10aae9     |
+---------------------+--------------------------------------+
~~~

You can see all floating ips available for your tenant with the folowing command:

~~~
$ neutron floatingip-list          
+--------------------------------------+------------------+---------------------+---------+
| id                                   | fixed_ip_address | floating_ip_address | port_id |
+--------------------------------------+------------------+---------------------+---------+
| 9ae7601a-5324-4830-81ee-194ed8a4d313 |                  | 172.16.42.3         |         |
+--------------------------------------+------------------+---------------------+---------+
~~~

Note no fixed ip is associated with the newly created floating IP. In order to use it, we have to associate the floating IP with the VM we desire to acess.

Neutron CLI allows to associate a floting ip to any kind of neutron port. Although this can be handy for several use cases, but to simply associate to a VM, it is easier with the openstack CLI as follows:


First list the servers and floating ips:
~~~
$ openstack server list                  
+--------------------------------------+------+--------+------------------+---------------------+
| ID                                   | Name | Status | Networks         | Image Name          |
+--------------------------------------+------+--------+------------------+---------------------+
| 90a7997e-e0a7-4f94-9c31-307c3d643170 | myvm | ACTIVE | paasnet=10.0.0.6 | Ubuntu-14.04-x86_64 |
+--------------------------------------+------+--------+------------------+---------------------+
$ openstack floating ip list
+--------------------------------------+---------------------+------------------+------+--------------------------------------+----------------------------------+
| ID                                   | Floating IP Address | Fixed IP Address | Port | Floating Network                     | Project                          |
+--------------------------------------+---------------------+------------------+------+--------------------------------------+----------------------------------+
| 9ae7601a-5324-4830-81ee-194ed8a4d313 | 172.16.42.3         | None             | None | a985b606-dacb-4561-baf6-d29fb9eb20bb | 2d423f42d64243e1b6aebb223d10aae9 |
+--------------------------------------+---------------------+------------------+------+--------------------------------------+----------------------------------+
~~~

Then associate them:
~~~
$ openstack server add floating ip myvm 172.16.42.3
~~~

The command does not return any output so check if it worked:

~~~
$ openstack server list
+--------------------------------------+------+--------+-------------------------------+---------------------+
| ID                                   | Name | Status | Networks                      | Image Name          |
+--------------------------------------+------+--------+-------------------------------+---------------------+
| 90a7997e-e0a7-4f94-9c31-307c3d643170 | myvm | ACTIVE | paasnet=10.0.0.6, 172.16.42.3 | Ubuntu-14.04-x86_64 |
+--------------------------------------+------+--------+-------------------------------+---------------------+
stack@stkt4-cp1-c0-m1-mgmt:~$ openstack floating ip list
+--------------------------------------+---------------------+------------------+--------------------------------------+--------------------------------------+----------------------------------+
| ID                                   | Floating IP Address | Fixed IP Address | Port                                 | Floating Network                     | Project                          |
+--------------------------------------+---------------------+------------------+--------------------------------------+--------------------------------------+----------------------------------+
| 9ae7601a-5324-4830-81ee-194ed8a4d313 | 172.16.42.3         | 10.0.0.6         | 70482aac-868d-4d53-add4-c3469031fae5 | a985b606-dacb-4561-baf6-d29fb9eb20bb | 2d423f42d64243e1b6aebb223d10aae9 |
+--------------------------------------+---------------------+------------------+--------------------------------------+--------------------------------------+----------------------------------+
~~~

Note that behind the scene the openstack CLI found the port used by the VM and used it to associate the port to the floating ip.


### Accessing the VM (not yet)

Now, if I try to ping the floating ip for my vm, it shoud work, right?

~~~
$ ping 172.16.42.3
PING 172.16.42.3 (172.16.42.3) 56(84) bytes of data.
^C
--- 172.16.42.3 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2014ms
~~~

Not so fast!!!

By default no flow comming from external networks is authorized into the private tentant networks. To access the VM, we still need create security rules authorizing these flows.


## Creating security group and rules

First of all, note that 'myvm' is on the default security group:

~~~
$ openstack server show myvm -c name -c security_groups
+-----------------+-------------------------+
| Field           | Value                   |
+-----------------+-------------------------+
| name            | myvm                    |
| security_groups | [{u'name': u'default'}] |
+-----------------+-------------------------+
~~~

Security groups can be specified on vm creation, but if not, the vm will be on the default one.

Check the rules on the default security group:

~~~
$ openstack security group show default
+-------------+------------------------------------------------------------------------------------------------------------------------------------------+
| Field       | Value                                                                                                                                    |
+-------------+------------------------------------------------------------------------------------------------------------------------------------------+
| description | Default security group                                                                                                                   |
| id          | 71248f43-68ea-4d8f-84d7-1cbb8aafaa94                                                                                                     |
| name        | default                                                                                                                                  |
| project_id  | 2d423f42d64243e1b6aebb223d10aae9                                                                                                         |
| rules       | direction='ingress', ethertype='IPv4', id='18faf833-f958-4f47-a582-7d6926e36626', remote_group_id='71248f43-68ea-4d8f-84d7-1cbb8aafaa94' |
|             | direction='ingress', ethertype='IPv6', id='6ad2b18d-eb34-4e97-8f66-ef597ffcd9ea', remote_group_id='71248f43-68ea-4d8f-84d7-1cbb8aafaa94' |
|             | direction='egress', ethertype='IPv6', id='d3c50607-8b6f-4ffd-a923-d9b151a56edd'                                                          |
|             | direction='egress', ethertype='IPv4', id='e0ee8b45-fc2b-48b0-a9e9-874eb61d4762'                                                          |
+-------------+------------------------------------------------------------------------------------------------------------------------------------------+
~~~

-> Note it only allows traffic in that is coming from the same security group. This means that any traffic coming in from any entity that is not on this seucirity group will be blocked.

It is possible to authorize external traffic directly on the defalt security grup, but let's create new group for that:

~~~
$ openstack security group create ext-group 
+-------------+---------------------------------------------------------------------------------+
| Field       | Value                                                                           |
+-------------+---------------------------------------------------------------------------------+
| description | ext-group                                                                       |
| headers     |                                                                                 |
| id          | fd316524-dcc6-428c-ac0b-26aa45f07b11                                            |
| name        | ext-group                                                                       |
| project_id  | 2d423f42d64243e1b6aebb223d10aae9                                                |
| rules       | direction='egress', ethertype='IPv4', id='80347d78-c790-470e-ac1e-ea2633063253' |
|             | direction='egress', ethertype='IPv6', id='38dc18ea-8010-4dd0-b357-0d105f4f1e06' |
+-------------+---------------------------------------------------------------------------------+
~~~

Authorized all external hosts to ping vms on this group:

~~~
$ openstack security group rule create --ingress --remote-ip 0.0.0.0/0 --protocol icmp ext-group+-------------------+--------------------------------------+
| Field             | Value                                |
+-------------------+--------------------------------------+
| direction         | ingress                              |
| ethertype         | IPv4                                 |
| headers           |                                      |
| id                | 67d7146c-2ddf-4bb2-8ac0-e60a1de823f0 |
| port_range_max    | None                                 |
| port_range_min    | None                                 |
| project_id        | 2d423f42d64243e1b6aebb223d10aae9     |
| protocol          | icmp                                 |
| remote_group_id   | None                                 |
| remote_ip_prefix  | 0.0.0.0/0                            |
| security_group_id | fd316524-dcc6-428c-ac0b-26aa45f07b11 |
+-------------------+--------------------------------------+
~~~

And now ping?

~~~
$ ping 172.16.42.3
PING 172.16.42.3 (172.16.42.3) 56(84) bytes of data.
^C
--- 172.16.42.3 ping statistics ---
11 packets transmitted, 0 received, 100% packet loss, time 10078ms
~~~

Not yet. The security group has to be added to the vm:

~~~
$ openstack server add security group myvm ext-group

$ ping 172.16.42.3
PING 172.16.42.3 (172.16.42.3) 56(84) bytes of data.
64 bytes from 172.16.42.3: icmp_seq=1 ttl=61 time=2.57 ms
64 bytes from 172.16.42.3: icmp_seq=2 ttl=61 time=0.446 ms
^C
--- 172.16.42.3 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.446/1.511/2.577/1.066 ms
~~~




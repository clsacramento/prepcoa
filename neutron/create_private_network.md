# Create private network

Private network is the network for a project. It is a virtual network for the VMs.

  NB: For the following example, it is important to be authenticated with a user of the tenant where the network is being created. In this case, the user will be paasadmin from 'paas' project.

## Creating the private network

~~~
$ neutron net-create paasnet
Created a new network:
+---------------------------+--------------------------------------+
| Field                     | Value                                |
+---------------------------+--------------------------------------+
| admin_state_up            | True                                 |
| id                        | 8ebd252c-5488-4600-aba3-2f7b4fcdff48 |
| mtu                       | 0                                    |
| name                      | paasnet                              |
| provider:network_type     | vxlan                                |
| provider:physical_network |                                      |
| provider:segmentation_id  | 1062                                 |
| router:external           | False                                |
| shared                    | False                                |
| status                    | ACTIVE                               |
| subnets                   |                                      |
| tenant_id                 | 2d423f42d64243e1b6aebb223d10aae9     |
+---------------------------+--------------------------------------+
~~~

## Creating a subnet on the private network

~~~
$ neutron subnet-create paasnet 10.0.0.0/24 --name paassubnet
Created a new subnet:
+-------------------+--------------------------------------------+
| Field             | Value                                      |
+-------------------+--------------------------------------------+
| allocation_pools  | {"start": "10.0.0.2", "end": "10.0.0.254"} |
| cidr              | 10.0.0.0/24                                |
| dns_nameservers   |                                            |
| enable_dhcp       | True                                       |
| gateway_ip        | 10.0.0.1                                   |
| host_routes       |                                            |
| id                | 4d6f2b18-6643-41b2-a71a-c98e204d2b5f       |
| ip_version        | 4                                          |
| ipv6_address_mode |                                            |
| ipv6_ra_mode      |                                            |
| name              | paassubnet                                 |
| network_id        | 8ebd252c-5488-4600-aba3-2f7b4fcdff48       |
| subnetpool_id     |                                            |
| tenant_id         | 2d423f42d64243e1b6aebb223d10aae9           |
+-------------------+--------------------------------------------+
~~~

## Crating a router

~~~
$ neutron router-create paasrouter
Created a new router:
+-----------------------+--------------------------------------+
| Field                 | Value                                |
+-----------------------+--------------------------------------+
| admin_state_up        | True                                 |
| distributed           | True                                 |
| external_gateway_info |                                      |
| ha                    | False                                |
| id                    | c065757c-8d6c-4f88-a0d6-52942f003a5b |
| name                  | paasrouter                           |
| routes                |                                      |
| status                | ACTIVE                               |
| tenant_id             | 2d423f42d64243e1b6aebb223d10aae9     |
+-----------------------+--------------------------------------
~~~

## Setting router gateway

The router gateway has to be setup to provide internet connection the VMs and also in order to be able to allocate floating ips.


Consindering the external network is named 'ext-net', run the following to set a router gateway:

~~~
$ neutron router-gateway-set paasrouter ext-net
Set gateway for router paasrouter
~~~

## Connecting the private network to the created router

Remember the private subnet created was named 'paassubnet'.

~~~
$ neutron router-interface-add paasrouter paassubnet
Added interface a7fe470e-c18d-4c7c-9a04-a3127c8f6b8c to router paasrouter.
~~~





# Creating External Net

In OpenStack the ext-net is very often used to provide external access for VMs. This is the networks that allows VMs to get a floating ip and be accessed from outside the cloud.

The creation this network depends also on the physical infrastructure where OpenStack is deployed.

A network in openstack is external if it is created using the flag 'router:external'. By default all external networks is visible of all tenants and most often the owner is the service tenant.

NB: openstack client for neutron is still limited so I'll be using neutron client for some operations.


Example:

## Creating network with 'router:external' flag
~~~
$ neutron net-create ext-net --router:external
Created a new network:
+---------------------------+--------------------------------------+
| Field                     | Value                                |
+---------------------------+--------------------------------------+
| admin_state_up            | True                                 |
| id                        | 1ef58329-e1e5-4306-9415-f311be0f6ca3 |
| mtu                       | 0                                    |
| name                      | ext-net                              |
| provider:network_type     | vxlan                                |
| provider:physical_network |                                      |
| provider:segmentation_id  | 1064                                 |
| router:external           | True                                 |
| shared                    | False                                |
| status                    | ACTIVE                               |
| subnets                   |                                      |
| tenant_id                 | 42b7713808514c139a31a153e433425b     |
+---------------------------+--------------------------------------+
~~~

Then create a subnet on ext-net. It is common to disable dhcp as address plan isually managed externally on this case. The gateway has also to be setup if it is not the first address on the subnet.

~~~
$ neutron subnet-create 1ef58329-e1e5-4306-9415-f311be0f6ca3 172.16.0.0/16 --disable-dhcp --gateway 172.16.255.254
Created a new subnet:
+-------------------+--------------------------------------------------+
| Field             | Value                                            |
+-------------------+--------------------------------------------------+
| allocation_pools  | {"start": "172.16.0.1", "end": "172.16.255.253"} |
| cidr              | 172.16.0.0/16                                    |
| dns_nameservers   |                                                  |
| enable_dhcp       | False                                            |
| gateway_ip        | 172.16.255.254                                   |
| host_routes       |                                                  |
| id                | 02f240db-1741-4b95-901b-91e03c550163             |
| ip_version        | 4                                                |
| ipv6_address_mode |                                                  |
| ipv6_ra_mode      |                                                  |
| name              |                                                  |
| network_id        | 1ef58329-e1e5-4306-9415-f311be0f6ca3             |
| subnetpool_id     |                                                  |
| tenant_id         | 42b7713808514c139a31a153e433425b                 |
+-------------------+--------------------------------------------------+
~~~

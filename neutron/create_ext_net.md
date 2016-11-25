# Creating External Net

In OpenStack the ext-net is very often used to provide external access for VMs. This is the networks that allows VMs to get a floating ip and be accessed from outside the cloud.

The creation of this network depends also on the physical infrastructure where OpenStack is deployed.

A network in openstack is external if it is created using the flag 'router:external'. By default all external networks is visible of all tenants and most often the owner is the service tenant.

NB: openstack client for neutron is still limited so I'll be using neutron client for some operations.


Example:

## Creating an external network with vlan provider

In the case where a single external network is provided for all tenants, very often they are vlan provided. The information about the physical network  has to be provided in OpenStack on create.

In this example, the external network is physical_network physnet1 is on vlan 42. 


~~~
$ neutron net-create ext-net --router:external --provider:network_type vlan --provider:segmentation_id 42 --provider:physical_network physnet1         
Created a new network:
+---------------------------+--------------------------------------+
| Field                     | Value                                |
+---------------------------+--------------------------------------+
| admin_state_up            | True                                 |
| id                        | a985b606-dacb-4561-baf6-d29fb9eb20bb |
| mtu                       | 0                                    |
| name                      | ext-net                              |
| provider:network_type     | vlan                                 |
| provider:physical_network | physnet1                             |
| provider:segmentation_id  | 42                                   |
| router:external           | True                                 |
| shared                    | False                                |
| status                    | ACTIVE                               |
| subnets                   |                                      |
| tenant_id                 | 42b7713808514c139a31a153e433425b     |
+---------------------------+--------------------------------------+
~~~

Optionally the flag '--shared' could have been added. As external networks are visible to all tenants for allocating floating ips, making it shared is not necessary. In fact, if the network is shared, any user can update the network so which is usually not desired.

  NB: To find out the name phisical_network name, check neutron configuration for bridge_mappings options which defines which physical interface on the hosts is mapped to a physical network.


## Creating a subnet for the external network

Then create a subnet on ext-net. It is common to disable dhcp as address plan isually managed externally on this case. The gateway can also be setup if it is not the first address on the subnet.

~~~
$ neutron subnet-create ext-net 172.16.0.0/16 --disable-dhcp --gateway 172.16.255.254
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

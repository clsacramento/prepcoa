# Deleting a network

In order to delete a network, make sure nothing is still plugged to it.

## First delete the network subnet

Make sure to delete all vms, routers or floating ips (if external) associated to the subnet.

### Checking subnet ports

Get the subnet id by doing 'neutron subnet-list'. With the id of the subnet, find out which ports are associated with it.

~~~
neutron port-list -D | grep 02f240db-1741-4b95-901b-91e03c550163
| e77a2918-5c96-4739-bc87-376404e5de73 |      | fa:16:3e:c2:9b:1f | {"subnet_id": "2e118c01-a697-4a1a-9f0e-1248f93ef815", "ip_address": "10.10.0.1"}   |
| 5db94222-3450-4432-b1a7-f1d2e871c564 |      | fa:16:3e:d9:ac:e6 | {"subnet_id": "2e118c01-a697-4a1a-9f0e-1248f93ef815", "ip_address": "10.10.0.2"}   |
| 65413cf0-082f-4ddb-a51e-e753e37ac075 |      | fa:16:3e:06:70:bb | {"subnet_id": "2e118c01-a697-4a1a-9f0e-1248f93ef815", "ip_address": "10.10.0.4"}   |
| 82534331-9eae-4cf7-b71a-d6e338d33b40 |      | fa:16:3e:19:8f:fb | {"subnet_id": "2e118c01-a697-4a1a-9f0e-1248f93ef815", "ip_address": "10.10.0.3"}   |

~~~

If no port shows up, this is good to delete the subnet. If only dhcp ports are on the network, this is ok. For example:

~~~
$ neutron port-show -F device_owner 82534331-9eae-4cf7-b71a-d6e338d33b40  
+--------------+--------------+
| Field        | Value        |
+--------------+--------------+
| device_owner | network:dhcp |
+--------------+--------------+
~~~


### Deleting a router

If a router is associated to the subnet, it has to be cleaned before.

Show the router:
~~~
neutron router-show 813be90d-9559-4ea8-8010-747dcf0a946a
+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field                 | Value                                                                                                                                                                                   |
+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| admin_state_up        | True                                                                                                                                                                                    |
| distributed           | True                                                                                                                                                                                    |
| external_gateway_info | {"network_id": "4c4fe776-106b-4088-b0d9-4fe282c1f923", "enable_snat": true, "external_fixed_ips": [{"subnet_id": "9154efa5-5033-4b53-9435-ac962374aa42", "ip_address": "172.31.0.21"}]} |
| ha                    | False                                                                                                                                                                                   |
| id                    | 813be90d-9559-4ea8-8010-747dcf0a946a                                                                                                                                                    |
| name                  | tempest-TemplateYAMLTestJSON-64085581-router                                                                                                                                            |
| routes                |                                                                                                                                                                                         |
| status                | ACTIVE                                                                                                                                                                                  |
| tenant_id             | 0555634a305e4c9aaf4966d25276934d                                                                                                                                                        |
+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
~~~


Delete any gateway on the router:
~~~
neutron router-gateway-clear 813be90d-9559-4ea8-8010-747dcf0a946a
Removed gateway from router 813be90d-9559-4ea8-8010-747dcf0a946a
~~~

List router ports:

~~~
neutron router-port-list 813be90d-9559-4ea8-8010-747dcf0a946a
+--------------------------------------+------+-------------------+----------------------------------------------------------------------------------+
| id                                   | name | mac_address       | fixed_ips                                                                        |
+--------------------------------------+------+-------------------+----------------------------------------------------------------------------------+
| e77a2918-5c96-4739-bc87-376404e5de73 |      | fa:16:3e:c2:9b:1f | {"subnet_id": "2e118c01-a697-4a1a-9f0e-1248f93ef815", "ip_address": "10.10.0.1"} |
+--------------------------------------+------+-------------------+----------------------------------------------------------------------------------+
~~~

Delete the port from the router:
neutron router-interface-delete router-id subnet-id
~~~
neutron router-interface-delete 813be90d-9559-4ea8-8010-747dcf0a946a 2e118c01-a697-4a1a-9f0e-1248f93ef815
Removed interface from router 813be90d-9559-4ea8-8010-747dcf0a946a.
~~~

Finally, delete it with the following command:

~~~
$ neutron router-delete 813be90d-9559-4ea8-8010-747dcf0a946a
Deleted router: 813be90d-9559-4ea8-8010-747dcf0a946a
~~~


If it is good to delete, run this:

~~~
$ neutron subnet-delete 02f240db-1741-4b95-901b-91e03c550163
Deleted subnet: 02f240db-1741-4b95-901b-91e03c550163
~~~


## Deleting the network

Now that the subnet has been deleted, it is possible to delete the subnet.

This can be verified with 'netron net-show network'.

For example:

~~~
$ neutron net-show ext-net
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

You can see subnets value is empty and so can run 'neutron subnet-delete'
~~~
$ neutron net-delete ext-net
Deleted network: 1ef58329-e1e5-4306-9415-f311be0f6ca3
~~~




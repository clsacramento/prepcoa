# Deleting a network

In order to delete a network, make sure nothing is still plugged to it.

## First delete the network subnet

Verifying it is empty:

Get the subnet id by doing 'neutron subnet-list'. With the id of the subnet, find out which ports are associated with it.

~~~
neutron port-list -D | grep 02f240db-1741-4b95-901b-91e03c550163
~~~

If no port shows up, this is good to delete the subnet:

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




# Quotas

OpenStack Quotas defines the amount of resources can be consumed by a tenant.

## Listing quota for a project

~~~
$ openstack quota show paas
+-----------------------+-------+
| Field                 | Value |
+-----------------------+-------+
| floating-ips          | 50    |
| healthmonitor         | -1    |
| ikepolicy             | -1    |
| ipsec_site_connection | -1    |
| ipsecpolicy           | -1    |
| listener              | -1    |
| loadbalancer          | 10    |
| network               | 10    |
| pool                  | 10    |
| port                  | 50    |
| rbac_policy           | 10    |
| router                | 10    |
| secgroup-rules        | 100   |
| secgroups             | 10    |
| subnet                | 10    |
| subnetpool            | -1    |
| vpnservice            | -1    |
+-----------------------+-------+
~~~

These are default values.

## Changing quota for a tenant

Example:

~~~
$ neutron quota-update --loadbalancer -1 --pool -1 --tenant-id  2d423f42d64243e1b6aebb223d10aae9 
+-----------------------+-------+
| Field                 | Value |
+-----------------------+-------+
| floatingip            | 50    |
| healthmonitor         | -1    |
| ikepolicy             | -1    |
| ipsec_site_connection | -1    |
| ipsecpolicy           | -1    |
| listener              | -1    |
| loadbalancer          | -1    |
| network               | 100   |
| pool                  | -1    |
| port                  | 500   |
| rbac_policy           | 10    |
| router                | 150   |
| security_group        | 100   |
| security_group_rule   | 1000  |
| subnet                | 100   |
| subnetpool            | -1    |
| vpnservice            | -1    |
+-----------------------+-------+
~~~

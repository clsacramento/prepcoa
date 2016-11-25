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


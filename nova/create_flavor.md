# Flavor

Flavor determines the resources that will be allocated to a vm (instance).

## Listing flavors
~~~
$ openstack flavor list
+----+-----------+-------+------+-----------+-------+-----------+
| ID | Name      |   RAM | Disk | Ephemeral | VCPUs | Is Public |
+----+-----------+-------+------+-----------+-------+-----------+
| 1  | m1.tiny   |   512 |    1 |         0 |     1 | True      |
| 2  | m1.small  |  2048 |   20 |         0 |     1 | True      |
| 3  | m1.medium |  4096 |   40 |         0 |     2 | True      |
| 4  | m1.large  |  8192 |   80 |         0 |     4 | True      |
| 5  | m1.xlarge | 16384 |  160 |         0 |     8 | True      |
+----+-----------+-------+------+-----------+-------+-----------+
~~~


## Creating a flavor

~~~
$ openstack flavor create  --id 6 --ram 32768 --disk 80 --vcpus 16 --public m1.xxl
+----------------------------+--------+
| Field                      | Value  |
+----------------------------+--------+
| OS-FLV-DISABLED:disabled   | False  |
| OS-FLV-EXT-DATA:ephemeral  | 0      |
| disk                       | 80     |
| id                         | 6      |
| name                       | m1.xxl |
| os-flavor-access:is_public | True   |
| ram                        | 32768  |
| rxtx_factor                | 1.0    |
| swap                       |        |
| vcpus                      | 16     |
+----------------------------+--------+
~~~

~~~
$ openstack flavor list
+----+-----------+-------+------+-----------+-------+-----------+
| ID | Name      |   RAM | Disk | Ephemeral | VCPUs | Is Public |
+----+-----------+-------+------+-----------+-------+-----------+
| 1  | m1.tiny   |   512 |    1 |         0 |     1 | True      |
| 2  | m1.small  |  2048 |   20 |         0 |     1 | True      |
| 3  | m1.medium |  4096 |   40 |         0 |     2 | True      |
| 4  | m1.large  |  8192 |   80 |         0 |     4 | True      |
| 5  | m1.xlarge | 16384 |  160 |         0 |     8 | True      |
| 6  | m1.xxl    | 32768 |   80 |         0 |    16 | True      |
+----+-----------+-------+------+-----------+-------+-----------+
~~~

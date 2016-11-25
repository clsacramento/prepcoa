# Create an instance

On openstack an instance is a VM.

Before creating a VM, get the following information that will be needed.

## Get image

~~~
$ openstack image list
+--------------------------------------+--------------------------+--------+
| ID                                   | Name                     | Status |
+--------------------------------------+--------------------------+--------+
| 555f8798-d122-47e4-b67f-fc3850b4628d | Ubuntu-14.04-x86_64      | active |
| cbe0a04d-1e12-4cd1-8783-f6a849ebecb5 | cirros-0.3.3-x86_64-disk | active |
+--------------------------------------+--------------------------+--------+
~~~

Image ID: 555f8798-d122-47e4-b67f-fc3850b4628d

## Get Flavor

In Openstack, the flavor defines how big is the VM, to get it:

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

Flavor ID: 2

## Creating a keypair

In this case, add a keypair of a previously generated key

~~~
$ openstack keypair create --public-key ~/mykey.pub mykey
+-------------+-------------------------------------------------+
| Field       | Value                                           |
+-------------+-------------------------------------------------+
| fingerprint | bd:35:a4:f3:70:c5:4e:56:31:e5:61:50:8a:6d:7b:bf |
| name        | mykey                                        |
| user_id     | d4e35a07820641278c69617a29b9b92f                |
+-------------+-------------------------------------------------+
~~~

Keypair: mykey


## Getting the private network

~~~ 
$ neutron net-list
+--------------------------------------+--------------------------------------+---------------------------------------+
| id                                   | name                                 | subnets                               |
+--------------------------------------+--------------------------------------+---------------------------------------+
| a985b606-dacb-4561-baf6-d29fb9eb20bb | ext-net                              | ef974aeb-4795-427f-9481-2cfdcfa86dc4  |
|                                      |                                      | 172.16.42.0/24                        |
| 8ebd252c-5488-4600-aba3-2f7b4fcdff48 | paasnet                              | 4d6f2b18-6643-41b2-a71a-c98e204d2b5f  |
|                                      |                                      | 10.0.0.0/24                           |
+--------------------------------------+--------------------------------------+---------------------------------------+
~~~

Private network ID: 8ebd252c-5488-4600-aba3-2f7b4fcdff48


## Creating the VM

Arguments:

Image ID: 555f8798-d122-47e4-b67f-fc3850b4628d

Flavor ID: 2

Keypair: mykey

Private subnet ID: 4d6f2b18-6643-41b2-a71a-c98e204d2b5f

~~~
$ openstack server create --image 555f8798-d122-47e4-b67f-fc3850b4628d --flavor 2 --key-name stackato --nic net-id=8ebd252c-5488-4600-aba3-2f7b4fcdff48 myvm
+--------------------------------------+------------------------------------------------------------+
| Field                                | Value                                                      |
+--------------------------------------+------------------------------------------------------------+
| OS-DCF:diskConfig                    | MANUAL                                                     |
| OS-EXT-AZ:availability_zone          |                                                            |
| OS-EXT-SRV-ATTR:host                 | None                                                       |
| OS-EXT-SRV-ATTR:hypervisor_hostname  | None                                                       |
| OS-EXT-SRV-ATTR:instance_name        | instance-0000000d                                          |
| OS-EXT-STS:power_state               | NOSTATE                                                    |
| OS-EXT-STS:task_state                | scheduling                                                 |
| OS-EXT-STS:vm_state                  | building                                                   |
| OS-SRV-USG:launched_at               | None                                                       |
| OS-SRV-USG:terminated_at             | None                                                       |
| accessIPv4                           |                                                            |
| accessIPv6                           |                                                            |
| addresses                            |                                                            |
| adminPass                            | HSwZHZWrpS3Z                                               |
| config_drive                         |                                                            |
| created                              | 2016-11-25T17:28:38Z                                       |
| flavor                               | m1.small (2)                                               |
| hostId                               |                                                            |
| id                                   | a757d202-0c4b-4be6-8a3d-f6e9d4fe419e                       |
| image                                | Ubuntu-14.04-x86_64 (555f8798-d122-47e4-b67f-fc3850b4628d) |
| key_name                             | mykey                                                      |
| name                                 | myvm                                                       |
| os-extended-volumes:volumes_attached | []                                                         |
| progress                             | 0                                                          |
| project_id                           | 2d423f42d64243e1b6aebb223d10aae9                           |
| properties                           |                                                            |
| security_groups                      | [{u'name': u'default'}]                                    |
| status                               | BUILD                                                      |
| updated                              | 2016-11-25T17:28:43Z                                       |
| user_id                              | d4e35a07820641278c69617a29b9b92f                           |
+--------------------------------------+------------------------------------------------------------+
~~~

## Wait until vm is Active

~~~

~~~


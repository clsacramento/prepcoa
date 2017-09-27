# Deleting a vm

## Show the vm

~~~
$ openstack server show myvm
+--------------------------------------+----------------------------------------------------------------------+
| Field                                | Value                                                                |
+--------------------------------------+----------------------------------------------------------------------+
| OS-DCF:diskConfig                    | MANUAL                                                               |
| OS-EXT-AZ:availability_zone          | nova                                                                 |
| OS-EXT-STS:power_state               | Running                                                              |
| OS-EXT-STS:task_state                | None                                                                 |
| OS-EXT-STS:vm_state                  | active                                                               |
| OS-SRV-USG:launched_at               | 2016-11-28T11:59:26.000000                                           |
| OS-SRV-USG:terminated_at             | None                                                                 |
| accessIPv4                           |                                                                      |
| accessIPv6                           |                                                                      |
| addresses                            | paasnet=10.0.0.6, 172.16.42.3                                        |
| config_drive                         |                                                                      |
| created                              | 2016-11-28T11:58:38Z                                                 |
| flavor                               | m1.small (2)                                                         |
| hostId                               | 88c4eca3afb66c67cd10555cbb9f9ae3f764872debe6f28f5f208395             |
| id                                   | 90a7997e-e0a7-4f94-9c31-307c3d643170                                 |
| image                                | 555f8798-d122-47e4-b67f-fc3850b4628d                                 |
| key_name                             | mykey                                                             |
| name                                 | myvm                                                                 |
| os-extended-volumes:volumes_attached | []                                                                   |
| progress                             | 0                                                                    |
| project_id                           | 2d423f42d64243e1b6aebb223d10aae9                                     |
| properties                           | image='62e4c4f7-8332-452b-af7a-e59f948ff0dd'                         |
| security_groups                      | [{u'name': u'default'}, {u'name': u'test'}, {u'name': u'ext-group'}] |
| status                               | ACTIVE                                                               |
| updated                              | 2016-11-28T15:03:24Z                                                 |
| user_id                              | d4e35a07820641278c69617a29b9b92f                                     |
+--------------------------------------+----------------------------------------------------------------------+
~~~

## Delete the vm

~~~
$ openstack server delete myvm

~~~

## Checking it was deleted

~~~
$ openstack server show myvm
No server with a name or ID of 'myvm' exists.
~~~

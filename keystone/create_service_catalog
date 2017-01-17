# Service Catalog


Service catalog provides endpoints to clients.

In order to create an endpoint, a service has to be created:

## Create a service

~~~
$ openstack service create --name cflab --description "paas endpoint"  paas
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | paas endpoint                    |
| enabled     | True                             |
| id          | afe49a0baeb74e6b87a4df1ecffc39a1 |
| name        | cflab                            |
| type        | paas                             |
+-------------+----------------------------------+
~~~

After creating a service, it automatically appears on the catalog but has no endpoint:
~~~
$ openstack catalog list
+-----------------+-----------------+-----------------------------------------------+
| Name            | Type            | Endpoints                                     |
+-----------------+-----------------+---------------------------------------------
| neutron         | network         | region1                                       |
|                 |                 |   admin: https://172.16.40.10:9696/           |
|                 |                 | region1                                       |
|                 |                 |   internal: https://172.16.40.10:9696/        |
|                 |                 | region1                                       |
|                 |                 |   public: https://172.16.41.5:9696/           |
|                 |                 |                                               |
| keystone        | identity        | region1                                       |
|                 |                 |   public: https://172.16.41.5:5000/v2.0       |
|                 |                 | region1                                       |
|                 |                 |   internal: https://172.16.40.10:5000/v2.0    |
|                 |                 | region1                                       |
|                 |                 |   admin: https://172.16.40.10:35357/v2.0      |
|                 |                 |                                               |
| nova            | compute         |                                               |
| swift           | object-store    |                                               |
| cinderv2        | volumev2        |                                               |
| glance          | image           | region1                                       |
|                 |                 |   admin: https://172.16.40.10:9292            |
|                 |                 | region1                                       |
|                 |                 |   public: https://172.16.41.5:9292            |
|                 |                 | region1                                       |
|                 |                 |   internal: https://172.16.40.10:9292         |
|                 |                 |                                               |
| cflab           | paas            |                                               |
| horizon         | dashboard       | region1                                       |
|                 |                 |   admin: https://172.16.40.10:443             |
|                 |                 | region1                                       |
|                 |                 |   public: https://172.16.41.5:443             |
|                 |                 | region1                                       |
|                 |                 |   internal: https://172.16.40.10:443          |
|                 |                 |                                               |
+-----------------+-----------------+-----------------------------------------------+
~~~

## Create an endpoint for a service
~~~
openstack endpoint create cflab internal https://172.16.40.50:443 --region region1
+--------------+----------------------------------+
| Field        | Value                            |
+--------------+----------------------------------+
| enabled      | True                             |
| id           | 8fc7ae5d9035441a8d1f81eb2c6cfa9a |
| interface    | internal                         |
| region       | region1                          |
| region_id    | region1                          |
| service_id   | afe49a0baeb74e6b87a4df1ecffc39a1 |
| service_name | cflab                            |
| service_type | paas                             |
| url          | https://172.16.40.50:443         |
+--------------+----------------------------------+
~~~

Verify the internal endpoint for cflab is now listed:
~~~
$ openstack endpoint list
+----------------------------------+---------+-----------------+-----------------+---------+-----------+-------------------------------------------------+
| ID                               | Region  | Service Name    | Service Type    | Enabled | Interface | URL                                             |
+----------------------------------+---------+-----------------+-----------------+---------+-----------+-------------------------------------------------+
| 0e345cba19574e399dd2bf0e81fc24c0 | region1 | horizon         | dashboard       | True    | admin     | https://172.16.40.10:443                        |
| 169ba85e343e4d838ba97d334fb306d9 | region1 | keystone        | identity        | True    | public    | https://172.16.41.5:5000/v2.0                   |
| 24a1ed4009b342b18a536e99ab9ee1db | region1 | swift           | object-store    | True    | internal  | https://172.16.40.10:8080/v1/AUTH_%(tenant_id)s |
| 2c32d9e0325746d4b1629dd94558e37b | region1 | horizon         | dashboard       | True    | public    | https://172.16.41.5:443                         |
| 3867a3d7c76047f89beec62bcfbe3990 | region1 | nova            | compute         | True    | internal  | https://172.16.40.10:8774/v2.1/%(tenant_id)s    |
| 47b67b1540984cc18dccf862c6f5ca4b | region1 | glance          | image           | True    | admin     | https://172.16.40.10:9292                       |
| 4931868b742b4422a9ad0b8f695cad1b | region1 | glance          | image           | True    | public    | https://172.16.41.5:9292                        |
| 4d6f0d37c24047659e838ade49efbef3 | region1 | swift           | object-store    | True    | public    | https://172.16.41.5:8080/v1/AUTH_%(tenant_id)s  |
| 534cee0939f84e149a4f8b595b54b865 | region1 | keystone        | identity        | True    | internal  | https://172.16.40.10:5000/v2.0                  |
| 5543bd0d9f37466a8d59b2df24ccc192 | region1 | heat            | orchestration   | True    | admin     | https://172.16.40.10:8004/v1/%(tenant_id)s      |
| 55801edbfd7f4b0cb655c0480f905ef9 | region1 | cinderv2        | volumev2        | True    | admin     | https://172.16.40.10:8776/v2/%(tenant_id)s      |
| 55928313fc5143dc86d33f69d27e195a | region1 | swift           | object-store    | True    | admin     | https://172.16.40.10:8080/v1/AUTH_%(tenant_id)s |
| 68ca32c3808f4c41bb6f0fa908f2b61a | region1 | cinder          | volume          | True    | public    | https://172.16.41.5:8776/v1/%(tenant_id)s       |
| 69e4d27cbbf744dab47afa1edd703347 | region1 | nova            | compute         | True    | admin     | https://172.16.40.10:8774/v2.1/%(tenant_id)s    |
| 6a0179abc7914f26a29d57a960538611 | region1 | cinder          | volume          | True    | internal  | https://172.16.40.10:8776/v1/%(tenant_id)s      |
| 8fc7ae5d9035441a8d1f81eb2c6cfa9a | region1 | cflab           | paas            | True    | internal  | https://172.16.40.50:443                        |
| 965ff9f95cf34a88b2f0f41d9557b396 | region1 | neutron         | network         | True    | admin     | https://172.16.40.10:9696/                      |
| a0bfaf2561bd4fdbbcf56e1c675453bf | region1 | neutron         | network         | True    | internal  | https://172.16.40.10:9696/                      |
| a393a8bd2ba74c4c80a9eca7f63408e5 | region1 | neutron         | network         | True    | public    | https://172.16.41.5:9696/                       |
| aad57ab132574d57b1adb09776c54fee | region1 | horizon         | dashboard       | True    | internal  | https://172.16.40.10:443                        |
| b30fe9a51f3644c985c87654ac30726f | region1 | cinder          | volume          | True    | admin     | https://172.16.40.10:8776/v1/%(tenant_id)s      |
| c7fcb3d582e64975977acfa3b049319a | region1 | nova            | compute         | True    | public    | https://172.16.41.5:8774/v2.1/%(tenant_id)s     |
| d9147685345942de85c81d62aae55426 | region1 | glance          | image           | True    | internal  | https://172.16.40.10:9292                       |
| dfd31cbcb0914413929f52a7693bb9b4 | region1 | keystone        | identity        | True    | admin     | https://172.16.40.10:35357/v2.0                 |
+----------------------------------+---------+-----------------+-----------------+---------+-----------+-------------------------------------------------+

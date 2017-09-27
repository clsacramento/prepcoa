# Object Storage

## Intro

List containers
~~~
# openstack container list
+---------+
| Name    |
+---------+
| myfiles |
+---------+
~~~

List objects in a container
~~~
# openstack object list myfiles
+-------------------------------+
| Name                          |
+-------------------------------+
| bootstrap-20161117T230131.log |
+-------------------------------+
~~~

Upload an object to a container
~~~
# touch test
# openstack object create myfiles test
+--------+-----------+----------------------------------+
| object | container | etag                             |
+--------+-----------+----------------------------------+
| test   | myfiles   | d41d8cd98f00b204e9800998ecf8427e |
+--------+-----------+----------------------------------+
~~~

Download object from container
~~~
# openstack object save --file myfiles-test myfiles test
# ls myfiles-test #--file is not specified it will use the object name as default
~~~

Show details about an object in a container
~~~
# openstack object show myfiles test
+----------------+---------------------------------------+
| Field          | Value                                 |
+----------------+---------------------------------------+
| account        | AUTH_2fa0fe127fdb40f68cdefde64e36c7fd |
| container      | myfiles                               |
| content-length | 0                                     |
| content-type   | application/octet-stream              |
| etag           | d41d8cd98f00b204e9800998ecf8427e      |
| last-modified  | Wed, 27 Sep 2017 10:54:34 GMT         |
| object         | test                                  |
+----------------+---------------------------------------+
~~~ 


# Create image

## First download an image from the web

Let's say ubuntu 14.04:

~~~
$ wget http://cloud-images.ubuntu.com/trusty/current/trusty-server-cloudimg-amd64.tar.gz
converted 'http://cloud-images.ubuntu.com/trusty/current/trusty-server-cloudimg-amd64.tar.gz' (ANSI_X3.4-1968) -> 'http://cloud-images.ubuntu.com/trusty/current/trusty-server-cloudimg-amd64.tar.gz' (UTF-8)
--2016-11-24 15:22:40--  http://cloud-images.ubuntu.com/trusty/current/trusty-server-cloudimg-amd64.tar.gz
Length: 241982197 (231M) [application/x-gzip]
Saving to: 'trusty-server-cloudimg-amd64.tar.gz'

trusty-server-cloudimg-amd64. 100%[==================================================>] 230.77M  6.53MB/s   in 63s    

2016-11-24 15:23:43 (3.64 MB/s) - 'trusty-server-cloudimg-amd64.tar.gz' saved [241982197/241982197]
~~~

First to list the images to see which ones are available so far:

~~~
$ openstack image list
+--------------------------------------+--------------------------+
| ID                                   | Name                     |
+--------------------------------------+--------------------------+
| cbe0a04d-1e12-4cd1-8783-f6a849ebecb5 | cirros-0.3.3-x86_64-disk |
+--------------------------------------+--------------------------+
~~~


Than create it from the downloaded file. This example is also making it public (only possible if the user is admin).

~~~
$ openstack image create --container-format bare --min-ram 256 --file trusty-server-cloudimg-amd64.tar.gz  --public Ubuntu-14.04-x86_64
+------------------+------------------------------------------------------+
| Field            | Value                                                |
+------------------+------------------------------------------------------+
| checksum         | 14191f5bee015b02dc7312fe56b5a9e3                     |
| container_format | bare                                                 |
| created_at       | 2016-11-24T15:44:33Z                                 |
| disk_format      | raw                                                  |
| file             | /v2/images/555f8798-d122-47e4-b67f-fc3850b4628d/file |
| id               | 555f8798-d122-47e4-b67f-fc3850b4628d                 |
| min_disk         | 0                                                    |
| min_ram          | 256                                                  |
| name             | Ubuntu-14.04-x86_64                                  |
| owner            | 42b7713808514c139a31a153e433425b                     |
| protected        | False                                                |
| schema           | /v2/schemas/image                                    |
| size             | 241982197                                            |
| status           | active                                               |
| updated_at       | 2016-11-24T15:44:48Z                                 |
| virtual_size     | None                                                 |
| visibility       | public                                               |
+------------------+------------------------------------------------------+
~~~


List the images again to see the ubuntu image was created:

~~~
$ openstack image list
+--------------------------------------+--------------------------+
| ID                                   | Name                     |
+--------------------------------------+--------------------------+
| 555f8798-d122-47e4-b67f-fc3850b4628d | Ubuntu-14.04-x86_64      |
| cbe0a04d-1e12-4cd1-8783-f6a849ebecb5 | cirros-0.3.3-x86_64-disk |
+--------------------------------------+--------------------------+
~~~

Show the image to see its details:

  -> Check its status is active

~~~
$ openstack image show 555f8798-d122-47e4-b67f-fc3850b4628d
+------------------+------------------------------------------------------+
| Field            | Value                                                |
+------------------+------------------------------------------------------+
| checksum         | 14191f5bee015b02dc7312fe56b5a9e3                     |
| container_format | bare                                                 |
| created_at       | 2016-11-24T15:44:33Z                                 |
| disk_format      | raw                                                  |
| file             | /v2/images/555f8798-d122-47e4-b67f-fc3850b4628d/file |
| id               | 555f8798-d122-47e4-b67f-fc3850b4628d                 |
| min_disk         | 0                                                    |
| min_ram          | 256                                                  |
| name             | Ubuntu-14.04-x86_64                                  |
| owner            | 42b7713808514c139a31a153e433425b                     |
| protected        | False                                                |
| schema           | /v2/schemas/image                                    |
| size             | 241982197                                            |
| status           | active                                               |
| updated_at       | 2016-11-24T15:44:48Z                                 |
| virtual_size     | None                                                 |
| visibility       | public                                               |
+------------------+------------------------------------------------------+
~~~


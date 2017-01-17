# Creating a tenant/project for use

Most often, after brand new installation of OpenStack, we use the service tenant to create a new tenants to actually use the cloud.

## Create tenant:

~~~
$ openstack project create paas
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description |                                  |
| domain_id   | default                          |
| enabled     | True                             |
| id          | 2d423f42d64243e1b6aebb223d10aae9 |
| is_domain   | False                            |
| name        | paas                             |
| parent_id   | None                             |
+-------------+----------------------------------+
~~~

## Create users on the project

~~~
openstack user create paasadmin --project paas
+--------------------+----------------------------------+
| Field              | Value                            |
+--------------------+----------------------------------+
| default_project_id | 2d423f42d64243e1b6aebb223d10aae9 |
| domain_id          | default                          |
| enabled            | True                             |
| id                 | d4e35a07820641278c69617a29b9b92f |
| name               | paasadmin                        |
+--------------------+----------------------------------+
$ openstack user create paasuser --project paas
+--------------------+----------------------------------+
| Field              | Value                            |
+--------------------+----------------------------------+
| default_project_id | 2d423f42d64243e1b6aebb223d10aae9 |
| domain_id          | default                          |
| enabled            | True                             |
| id                 | d4ea6d23e2454e158f30e9668953ff33 |
| name               | paasuser                         |
+--------------------+----------------------------------+
~~~

### Set password for created users

~~~
$ openstack user set --password userpass paasuser
$ openstack user set --password-prompt paasadmin
User Password:
Repeat User Password:
~~~

## Assigning roles

The users that were created have the 'paas' project as default but they don't actually have a role with that project.

Because of that, if someone tries to authenticate with paasuser or paasadmin, he will get this message 'You are not authorized for any projects or domains.'

To fix this, it is necessary to assign at list one role for each user on a project. User roles is the way to determine what each user can do on OpenStack, they are defined on the 'policy.json' configuration for each openstack project.


### Listing available roles

~~~
$ openstack role list
+----------------------------------+---------------------------+
| ID                               | Name                      |
+----------------------------------+---------------------------+
| 1ab373e54a9d4e048ef5295fedf4c354 | KeystoneAdmin             |
| 3c3cd0be44664896bd2ffde53504c5b2 | service                   |
| 49d7b83d348441d1b51417a8ac470419 | admin                     |
| 6ed9706a58eb4f5ca0e632626dd0dfb5 | Member                    |
| 82fc9047494a4c48b7c806af20ad5232 | heat_stack_user           |
| 955fa1fdda434e138bd6df6635c91bf8 | monasca-agent             |
| 9b2899f998184ac68c1973214b790658 | neutron_admin             |
| 9ed0916a8c55452ab5c899d8f807b508 | monasca-user              |
| 9f21712bdeb445769cf8c426bd0a4bf0 | designate_admin           |
| c756bf133e3c4ad9abafa54fb18c148e | glance_admin              |
| dd9132c94e484d3a88c524c9acab5407 | swiftoperator             |
| e1f6323bd9274c17a34804249cba1223 | nova_admin                |
| eac0003122514896af9af30505278761 | ResellerAdmin             |
| f046db1fb66e4ca6be3e5eb1ea1b269c | cinder_admin              |
+----------------------------------+---------------------------+
~~~

For this example will add both user as member of the project. And also add admin role to the admin user.

~~~
$ openstack role add --project paas --user paasuser Member
$ openstack role add --project paas --user paasadmin Member
$ openstack role add --project paas --user paasadmin admin
~~~

### Checking role assignments

To check the roles were added to the users, run:

~~~
$ openstack role assignment list --project paas
+----------------------------+----------------------------+-------+------------------------------+--------+-----------+
| Role                       | User                       | Group | Project                      | Domain | Inherited |
+----------------------------+----------------------------+-------+------------------------------+--------+-----------+
| 49d7b83d348441d1b51417a8ac | d4e35a07820641278c69617a29 |       | 2d423f42d64243e1b6aebb223d10 |        | False     |
| 470419                     | b9b92f                     |       | aae9                         |        |           |
| 6ed9706a58eb4f5ca0e632626d | d4e35a07820641278c69617a29 |       | 2d423f42d64243e1b6aebb223d10 |        | False     |
| d0dfb5                     | b9b92f                     |       | aae9                         |        |           |
| 6ed9706a58eb4f5ca0e632626d | d4ea6d23e2454e158f30e96689 |       | 2d423f42d64243e1b6aebb223d10 |        | False     |
| d0dfb5                     | 53ff33                     |       | aae9                         |        |           |
+----------------------------+----------------------------+-------+------------------------------+--------+-----------+
~~~

# Groups

User groups is an aggregation of users from different projects.

## Creating groups
~~~
$ openstack group create --domain default coolusers
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description |                                  |
| domain_id   | default                          |
| id          | 0de2fb09bb37466fb5e97b8ddaa9c31e |
| name        | coolusers                        |
+-------------+----------------------------------+
~~~
~~~
openstack group list
+----------------------------------+-----------+
| ID                               | Name      |
+----------------------------------+-----------+
| 0de2fb09bb37466fb5e97b8ddaa9c31e | coolusers |
+----------------------------------+-----------+
~~~

## Adding users to group
~~~
$ openstack group add user coolusers admin
admin added to group coolusers
$ openstack group add user coolusers demo 
demo added to group coolusers
$ openstack group add user coolusers paasadmin
paasadmin added to group coolusers
~~~


## Adding a role to a group
Having created a group, it is possible to assign a role to all members of that group. Instead of manually adding it for each user. Removing a group of the group, revokes the role granted to the user through the group.
~~~
$ openstack role add --group coolusers --project services _member_
$ openstack role assignment list --group coolusers
+----------------------------------+------+----------------------------------+----------------------------------+--------+-----------+
| Role                             | User | Group                            | Project                          | Domain | Inherited |
+----------------------------------+------+----------------------------------+----------------------------------+--------+-----------+
| 9fe2ff9ee4384b1894a90878d3e92bab |      | 0de2fb09bb37466fb5e97b8ddaa9c31e | 9f27b74f5f094b7e9069c80c3a2c1a43 |        | False     |
+----------------------------------+------+----------------------------------+----------------------------------+--------+-----------+
$ openstack role show 9fe2ff9ee4384b1894a90878d3e92bab
+-------------+-----------------------------------------------+
| Field       | Value                                         |
+-------------+-----------------------------------------------+
| description | Bootstrap  roles  created via keystone deploy |
| domain_id   | None                                          |
| id          | 9fe2ff9ee4384b1894a90878d3e92bab              |
| name        | _member_                                      |
+-------------+-----------------------------------------------+
$ openstack project show 9f27b74f5f094b7e9069c80c3a2c1a43
+-------------+------------------------------------------------+
| Field       | Value                                          |
+-------------+------------------------------------------------+
| description | Bootstrap accounts created via keystone deploy |
| domain_id   | default                                        |
| enabled     | True                                           |
| id          | 9f27b74f5f094b7e9069c80c3a2c1a43               |
| is_domain   | False                                          |
| name        | services                                       |
| parent_id   | default                                        |
+-------------+------------------------------------------------+
~~~

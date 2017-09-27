# OpenStack Orchestration (Heat)

HOT is a template format supported by the heat, along with the other template format, i.e. the Heat CloudFormation-compatible format (CFN). This guide is targeted towards template authors and explains how to write HOT templates based on examples. A detailed specification of HOT can be found at Heat Orchestration Template (HOT) specification.

Doc: https://docs.openstack.org/heat/latest/template_guide/hot_guide.html

TIP: to find this doc, search for 'heat orchestration' on docs.openstack.org (this is allowed).

In this page you get an example of a template so you can re-use.

## Creating a VM with Heat


~~~
# cat vm.yml
#
# This is a hello world HOT template just defining a single compute
# server. Add a db_port variable to a tmp file to show how to execute
# commands on the VM after it is created.
#
heat_template_version: 2013-05-23

description: >
  Hello world HOT template that just defines a single server.
  Contains just base features to verify base HOT support.

parameters:
  key_name:
    type: string
    description: Name of an existing key pair to use for the server
    default: mykey
    constraints:
      - custom_constraint: nova.keypair
  flavor:
    type: string
    description: Flavor for the server to be created
    default: m1.large
    constraints:
      - custom_constraint: nova.flavor
  image:
    type: string
    description: Image ID or image name to use for the server
    default: CentOS-7-x86_64 
    constraints:
      - custom_constraint: glance.image
  network:
    type: string
    description: Name or id of the network
    default: priv2
    constraints:
      - custom_constraint: neutron.network
  admin_pass:
    type: string
    description: Admin password
    hidden: true
    default: centos
    constraints:
      - length: { min: 6, max: 8 }
        description: Password length must be between 6 and 8 characters
      - allowed_pattern: "[a-zA-Z0-9]+"
        description: Password must consist of characters and numbers only
  db_port:
    type: number
    description: Database port number / keeping this just for test
    default: 50000
    constraints:
      - range: { min: 40000, max: 60000 }
        description: Port number must be between 40000 and 60000

resources:
  server:
    type: OS::Nova::Server
    properties:
      key_name: { get_param: key_name }
      image: { get_param: image }
      flavor: { get_param: flavor }
      admin_pass: { get_param: admin_pass }
      networks: [{network: { get_param: network}}]
      user_data:
        str_replace:
          template: |
            #!/bin/bash
            echo db_port | /tmp/db_port
          params:
            db_port: { get_param: db_port }

outputs:
  server_networks:
    description: The networks of the deployed server
    value: { get_attr: [server, networks] }
~~~

create a parameters file to override the defaults from the template (mandatory when no default provided):
~~~
# cat parameters 
parameters:
  flavor: m1.small
  network: private
  db_port: 1
~~~

Check for syntax:
~~~
# openstack stack create -e parameters -t vm.yml --dry-run teststack
ERROR: Parameter 'db_port' is invalid: Port number must be between 40000 and 60000
~~~

The parameter provided violated one of the parameters rule, fix it:
~~~
parameters:
  flavor: m1.small
  network: private
  db_port: 1
~~~

Try again:
~~~
# openstack stack create -e parameters -t vm.yml --dry-run teststack
+-----------------------+--------------------------------------------------------------------------------------------+
| Field                 | Value                                                                                      |
+-----------------------+--------------------------------------------------------------------------------------------+
| capabilities          | []                                                                                         |
| creation_time         | 2017-09-27T15:13:58Z                                                                       |
| deletion_time         | None                                                                                       |
| description           | Hello world HOT template that just defines a single                                        |
|                       | server. Contains just base features to verify base HOT                                     |
|                       | support.                                                                                   |
| disable_rollback      | True                                                                                       |
| id                    | None                                                                                       |
| links                 | https://172.16.101.5:8004/v1/2fa0fe127fdb40f68cdefde64e36c7fd/stacks/teststack/None (self) |
| notification_topics   | []                                                                                         |
| outputs               | [                                                                                          |
|                       |   {                                                                                        |
|                       |     "output_value": null,                                                                  |
|                       |     "output_key": "server_networks",                                                       |
|                       |     "description": "The networks of the deployed server"                                   |
|                       |   }                                                                                        |
|                       | ]                                                                                          |
| parameters            | {                                                                                          |
|                       |   "OS::project_id": "2fa0fe127fdb40f68cdefde64e36c7fd",                                    |
|                       |   "network": "private",                                                                    |
|                       |   "OS::stack_name": "teststack",                                                           |
|                       |   "admin_pass": "******",                                                                  |
|                       |   "key_name": "mykey",                                                                  |
|                       |   "image": "CentOS-7-x86_64",                                                              |
|                       |   "db_port": "50000",                                                                      |
|                       |   "OS::stack_id": "None",                                                                  |
|                       |   "flavor": "m1.small"                                                                     |
|                       | }                                                                                          |
| parent                | None                                                                                       |
| resources             | [                                                                                          |
|                       |   {                                                                                        |
|                       |     "resource_name": "server",                                                             |
|                       |     "resource_identity": {                                                                 |
|                       |       "stack_name": "teststack",                                                           |
|                       |       "stack_id": "None",                                                                  |
|                       |       "tenant": "2fa0fe127fdb40f68cdefde64e36c7fd",                                        |
|                       |       "path": "/resources/server"                                                          |
|                       |     },                                                                                     |
|                       |     "description": "",                                                                     |
|                       |     "stack_identity": {                                                                    |
|                       |       "stack_name": "teststack",                                                           |
|                       |       "stack_id": "None",                                                                  |
|                       |       "tenant": "2fa0fe127fdb40f68cdefde64e36c7fd",                                        |
|                       |       "path": ""                                                                           |
|                       |     },                                                                                     |
|                       |     "stack_name": "teststack",                                                             |
|                       |     "creation_time": null,                                                                 |
|                       |     "resource_status": "COMPLETE",                                                         |
|                       |     "updated_time": null,                                                                  |
|                       |     "resource_type": "OS::Nova::Server",                                                   |
|                       |     "required_by": [],                                                                     |
|                       |     "resource_status_reason": "",                                                          |
|                       |     "physical_resource_id": "",                                                            |
|                       |     "attributes": {                                                                        |
|                       |       "console_urls": "",                                                                  |
|                       |       "name": null,                                                                        |
|                       |       "first_address": "",                                                                 |
|                       |       "instance_name": "",                                                                 |
|                       |       "accessIPv4": "",                                                                    |
|                       |       "accessIPv6": "",                                                                    |
|                       |       "networks": "",                                                                      |
|                       |       "addresses": ""                                                                      |
|                       |     },                                                                                     |
|                       |     "properties": {                                                                        |
|                       |       "admin_pass": "centos",                                                              |
|                       |       "user_data_update_policy": "REPLACE",                                                |
|                       |       "availability_zone": null,                                                           |
|                       |       "image": "CentOS-7-x86_64",                                                          |
|                       |       "user_data": "#!/bin/bash\necho 50000\n",                                            |
|                       |       "diskConfig": null,                                                                  |
|                       |       "flavor_update_policy": "RESIZE",                                                    |
|                       |       "flavor": "m1.small",                                                                |
|                       |       "reservation_id": null,                                                              |
|                       |       "networks": [                                                                        |
|                       |         {                                                                                  |
|                       |           "subnet": null,                                                                  |
|                       |           "uuid": null,                                                                    |
|                       |           "fixed_ip": null,                                                                |
|                       |           "floating_ip": null,                                                             |
|                       |           "port_extra_properties": null,                                                   |
|                       |           "port": null,                                                                    |
|                       |           "network": "private"                                                             |
|                       |         }                                                                                  |
|                       |       ],                                                                                   |
|                       |       "security_groups": [],                                                               |
|                       |       "scheduler_hints": null,                                                             |
|                       |       "config_drive": null,                                                                |
|                       |       "personality": {},                                                                   |
|                       |       "user_data_format": "HEAT_CFNTOOLS",                                                 |
|                       |       "admin_user": null,                                                                  |
|                       |       "block_device_mapping": null,                                                        |
|                       |       "key_name": "mykey",                                                              |
|                       |       "software_config_transport": "POLL_SERVER_CFN",                                      |
|                       |       "name": null,                                                                        |
|                       |       "block_device_mapping_v2": null,                                                     |
|                       |       "image_update_policy": "REBUILD",                                                    |
|                       |       "metadata": null                                                                     |
|                       |     },                                                                                     |
|                       |     "resource_action": "INIT",                                                             |
|                       |     "metadata": {}                                                                         |
|                       |   }                                                                                        |
|                       | ]                                                                                          |
| stack_name            | teststack                                                                                  |
| stack_owner           | None                                                                                       |
| stack_user_project_id | None                                                                                       |
| tags                  | None                                                                                       |
| template_description  | Hello world HOT template that just defines a single                                        |
|                       | server. Contains just base features to verify base HOT                                     |
|                       | support.                                                                                   |
| timeout_mins          | None                                                                                       |
| updated_time          | None                                                                                       |
+-----------------------+--------------------------------------------------------------------------------------------+
~~~

Now that it seems ok, let's create the stack:

To create a stack:
~~~
openstack stack create -e parameters -t vm.yml teststack
~~~
We will be using the '--wait' option that will print progress on the output and finish when all resources of the stack is created:
~~~
# openstack stack create -e parameters -t vm.yml --wait teststack
2017-09-27 15:31:35Z [teststack]: CREATE_IN_PROGRESS  Stack CREATE started
2017-09-27 15:31:38Z [teststack.server]: CREATE_IN_PROGRESS  state changed
2017-09-27 15:32:10Z [teststack.server]: CREATE_COMPLETE  state changed
2017-09-27 15:32:10Z [teststack]: CREATE_COMPLETE  Stack CREATE completed successfully
+---------------------+---------------------------------------------------------------------------------------------------------------------+
| Field               | Value                                                                                                               |
+---------------------+---------------------------------------------------------------------------------------------------------------------+
| id                  | e2959898-d328-4111-9460-06520c890429                                                                                |
| stack_name          | teststack                                                                                                           |
| description         | Hello world HOT template that just defines a single server. Contains just base features to verify base HOT support. |
|                     |                                                                                                                     |
| creation_time       | 2017-09-27T15:31:34Z                                                                                                |
| updated_time        | None                                                                                                                |
| stack_status        | CREATE_COMPLETE                                                                                                     |
| stack_status_reason | Stack CREATE completed successfully                                                                                 |
+---------------------+---------------------------------------------------------------------------------------------------------------------+
~~~


Obtaining the stack output:
~~~
# openstack stack output list teststack
+-----------------+-------------------------------------+
| output_key      | description                         |
+-----------------+-------------------------------------+
| server_networks | The networks of the deployed server |
+-----------------+-------------------------------------+
# openstack stack output show teststack server_networks
+--------------+---------------------------------------------+
| Field        | Value                                       |
+--------------+---------------------------------------------+
| description  | The networks of the deployed server         |
| output_key   | server_networks                             |
| output_value | {                                           |
|              |   "560195e7-5a82-4b58-8e87-6dd47bcace30": [ |
|              |     "10.0.0.4"                              |
|              |   ],                                        |
|              |   "private": [                              |
|              |     "10.0.0.4"                              |
|              |   ]                                         |
|              | }                                           |
+--------------+---------------------------------------------+

~~~

Checking VM created by the stack:

~~~
# openstack stack resource list teststack
+---------------+--------------------------------------+------------------+-----------------+----------------------+
| resource_name | physical_resource_id                 | resource_type    | resource_status | updated_time         |
+---------------+--------------------------------------+------------------+-----------------+----------------------+
| server        | ae085cf7-ec6b-4934-a578-9c619f0d9610 | OS::Nova::Server | CREATE_COMPLETE | 2017-09-27T15:31:35Z |
+---------------+--------------------------------------+------------------+-----------------+----------------------+

# openstack server show ae085cf7-ec6b-4934-a578-9c619f0d9610
+-----------------------------+----------------------------------------------------------+
| Field                       | Value                                                    |
+-----------------------------+----------------------------------------------------------+
| OS-DCF:diskConfig           | MANUAL                                                   |
| OS-EXT-AZ:availability_zone | nova                                                     |
| OS-EXT-STS:power_state      | Running                                                  |
| OS-EXT-STS:task_state       | None                                                     |
| OS-EXT-STS:vm_state         | active                                                   |
| OS-SRV-USG:launched_at      | 2017-09-27T15:32:07.000000                               |
| OS-SRV-USG:terminated_at    | None                                                     |
| accessIPv4                  |                                                          |
| accessIPv6                  |                                                          |
| addresses                   | private=10.0.0.4                                         |
| config_drive                |                                                          |
| created                     | 2017-09-27T15:31:41Z                                     |
| flavor                      | m1.small (2)                                             |
| hostId                      | 409e44b5b906261de8836c9d637e97c1256a9b890060c19d534bfb70 |
| id                          | ae085cf7-ec6b-4934-a578-9c619f0d9610                     |
| image                       | CentOS-7-x86_64 (3d340ada-e65d-4669-8589-ab8317633ac9)   |
| key_name                    | mykey                                                 |
| name                        | teststack-server-nzidhufjh7r7                            |
| progress                    | 0                                                        |
| project_id                  | 2fa0fe127fdb40f68cdefde64e36c7fd                         |
| properties                  |                                                          |
| security_groups             | name='default'                                           |
| status                      | ACTIVE                                                   |
| updated                     | 2017-09-27T15:32:07Z                                     |
| user_id                     | 23bc795243c2467baef22c7cc1ed3771                         |
| volumes_attached            |                                                          |
+-----------------------------+----------------------------------------------------------+
~~~


## Template that create multiple VMs

~~~
# cat vmsgroup.yml 
#
# This is a template to deploy a number of servers on an existing
# private network.
#
heat_template_version: 2013-05-23

description: >
  This is a template to deploy a number of servers on an existing
  private network.

parameters:
  key_name:
    type: string
    description: Name of an existing key pair to use for the server
    default: mykey
    constraints:
      - custom_constraint: nova.keypair
  flavor:
    type: string
    description: Flavor for the server to be created
    default: m1.large
    constraints:
      - custom_constraint: nova.flavor
  image:
    type: string
    description: Image ID or image name to use for the server
    default: CentOS-7-x86_64
    constraints:
      - custom_constraint: glance.image
  network:
    type: string
    description: Name or id of the network
    default: priv2
    constraints:
      - custom_constraint: neutron.network
  admin_pass:
    type: string
    description: Admin password
    hidden: true
    default: centos
    constraints:
      - length: { min: 6, max: 8 }
        description: Password length must be between 6 and 8 characters
      - allowed_pattern: "[a-zA-Z0-9]+"
        description: Password must consist of characters and numbers only
  cluster_name:
    type: string
    description: cluster name
    default: heatkube

  count:
    type: number
    description: number of vms to deploy (1 master plus n - 1 workers)
    default: 3

resources:
  kubevms:
    type: OS::Heat::ResourceGroup
    properties:
      count: { get_param: count }
      resource_def:
        type: OS::Nova::Server
        properties:
          key_name: { get_param: key_name }
          image: { get_param: image }
          flavor: { get_param: flavor }
          admin_pass: { get_param: admin_pass }
          name:
            str_replace:
              template: $name-%index%
              params:
                $name: { get_param: cluster_name }
          networks: [{network: { get_param: network}}]


outputs:
  servers:
    description: The name of servers deployed
    value: { get_attr: [kubevms, name] }

  servers_net:
    description: The names and ips of the deployed servers
    value: [{ get_attr: [kubevms, name] }, { get_attr: [kubevms, first_address] }] 

  servers_ip:
    description: The ips of servers deployed
    value: { get_attr: [kubevms, first_address] }

~~~

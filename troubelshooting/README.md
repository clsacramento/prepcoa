#Troubleshooting

## Backup OpenStack DB

~~~
# mysqldump --opt --all-databases > /tmp/all-openstack.sql
~~~

### Backup single OpenStack DB

~~~
# mysqldump --opt neutron > /tmp/neutron.sql
~~~

## Nova Status

### List Hosts
~~~
$ openstack host list
+--------------------+-------------+----------+
| Host Name          | Service     | Zone     |
+--------------------+-------------+----------+
| centos7.test.local | cert        | internal |
| centos7.test.local | consoleauth | internal |
| centos7.test.local | scheduler   | internal |
| centos7.test.local | conductor   | internal |
| centos7.test.local | compute     | nova     |
+--------------------+-------------+----------+
~~~

### See status of compute
~~~
$ nova hypervisor-list
+----+---------------------+-------+---------+
| ID | Hypervisor hostname | State | Status  |
+----+---------------------+-------+---------+
| 1  | centos7.test.local  | up    | enabled |
+----+---------------------+-------+---------+
~~~

### Details of a host
~~~
$ openstack host show centos7.test.local
+--------------------+-------------------------+-----+-----------+---------+
| Host               | Project                 | CPU | Memory MB | Disk GB |
+--------------------+-------------------------+-----+-----------+---------+
| centos7.test.local | (total)                 |   1 |      3952 |      49 |
| centos7.test.local | (used_now)              |   1 |      1024 |       1 |
| centos7.test.local | (used_max)              |   1 |       512 |       1 |
| centos7.test.local | ae8d3e3f5cff4a959f1ae1c |   1 |       512 |       1 |
|                    | fe9e80d6d               |     |           |         |
+--------------------+-------------------------+-----+-----------+---------+
~~~

## Analyze Messaging Servers
### RabbitMQ Status
~~~
# rabbitmqctl status
Status of node rabbit@centos7 ...
[{pid,972},
 {running_applications,[{rabbit,"RabbitMQ","3.3.5"},
                        {os_mon,"CPO  CXC 138 46","2.2.14"},
                        {xmerl,"XML parser","1.3.6"},
                        {mnesia,"MNESIA  CXC 138 12","4.11"},
                        {sasl,"SASL  CXC 138 11","2.3.4"},
                        {stdlib,"ERTS  CXC 138 10","1.19.4"},
                        {kernel,"ERTS  CXC 138 10","2.16.4"}]},
 {os,{unix,linux}},
 {erlang_version,"Erlang R16B03-1 (erts-5.10.4) [source] [64-bit] [async-threads:30] [hipe] [kernel-poll:true]\n"},
 {memory,[{total,211601864},
          {connection_procs,2653544},
          {queue_procs,1273056},
          {plugins,0},
          {other_proc,13363136},
          {mnesia,363184},
          {mgmt_db,0},
          {msg_index,86264},
          {other_ets,916560},
          {binary,170846960},
          {code,16700042},
          {atom,602729},
          {other_system,4796389}]},
 {alarms,[]},
 {listeners,[{clustering,25672,"::"},{amqp,5672,"::"}]},
 {vm_memory_high_watermark,0.4},
 {vm_memory_limit,1657934643},
 {disk_free_limit,50000000},
 {disk_free,50450022400},
 {file_descriptors,[{total_limit,16284},
                    {total_used,68},
                    {sockets_limit,14653},
                    {sockets_used,66}]},
 {processes,[{limit,1048576},{used,794}]},
 {run_queue,0},
 {uptime,9767}]
...done.
~~~

### List users

~~~
# rabbitmqctl list_users
Listing users ...
guest   [administrator]
...done.
~~~

## Network Status

### Neutron agents

~~~
$ neutron agent-list
+------+--------------+--------+----------------+-----+-------------+-----------------------+
| id   |agent_type     |host   |availability_zone|alive|admin_state_up |binary              |
+------+--------------+--------+----------------+-----+-------------+-----------------------+
| 384..|Open vSwitch  |centos7|                | :-) | True          |neutron-openvswitch-  |     
| 665..|L3 agent      |centos7|nova            | :-) | True          |neutron-l3-agent      |
| 7c4..|Metering agent|centos7|                | :-) | True          |neutron-metering-agent|
| 82b..|DHCP agent    |centos7|nova            | :-) | True          |neutron-dhcp-agent    |
| 9b8..|Metadata agent|centos7|                | :-) | True          |neutron-metadata-agent|
+------+--------------+--------+----------------+-----+-------------+----------------------+
~~~


## OpenStack services
~~~
[root@centos7 ∼]# systemctl | grep openstack
  openstack-aodh-evaluator.service loaded active running OpenStack Alarm evaluator service
  openstack-aodh-listener.service  loaded active running OpenStack Alarm listener service
  openstack-aodh-notifier.service  loaded active running OpenStack Alarm notifier service
  openstack-ceilometer-central.service loaded active running OpenStack ceilometer central agent
  openstack-ceilometer-collector.service loaded active running OpenStack ceilometer collection service
  openstack-ceilometer-compute.service loaded active running OpenStack ceilometer compute agent
  openstack-ceilometer-notification.service loaded active running OpenStack ceilometer notification agent
  openstack-cinder-api.service loaded active running OpenStack Cinder API Server
  openstack-cinder-backup.service loaded active running OpenStack Cinder Backup Server
  openstack-cinder-scheduler.service loaded active running OpenStack Cinder Scheduler Server
  openstack-cinder-volume.service loaded active running OpenStack Cinder Volume Server
  openstack-glance-api.service loaded active running OpenStack Image Service (code-named Glance) API server
  openstack-glance-registry.service loaded active running OpenStack Image Service (code-named Glance) Registry server
  openstack-gnocchi-metricd.service loaded active running OpenStack gnocchi metricd service
  openstack-gnocchi-statsd.service loaded active running OpenStack gnocchi statsd service
  openstack-losetup.service loaded active exited Setup cinder-volume loop device
  openstack-nova-api.service loaded active running OpenStack Nova API Server
  openstack-nova-cert.service loaded active running OpenStack Nova Cert Server
  openstack-nova-compute.service loaded active running OpenStack Nova Compute Server
  openstack-nova-conductor.service loaded active running OpenStack Nova Conductor Server
  openstack-nova-consoleauth.service loaded active running OpenStack Nova VNC console auth Server
  openstack-nova-novncproxy.service loaded active running OpenStack Nova NoVNC Proxy Server
  openstack-nova-scheduler.service loaded active running   OpenStack Nova Scheduler Server
~~~


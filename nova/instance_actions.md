# Instance Actions


Once a VM is created, these are the actions that can be taken on it

 * server delete  Delete server(s)
 * server lock    Lock a server. A non-admin user will not be able to execute actions
 * server migrate  Migrate server to different host
 * server pause   Pause server
 * server reboot  Perform a hard or soft server reboot
 * server rebuild  Rebuild server
 * server rescue  Put server in rescue mode
 * server resize  Scale server to a new flavor
 * server resume  Resume server
 * server ssh     Ssh to server
 * server suspend  Suspend server
 * server unlock  Unlock server
 * server unpause  Unpause server
 * server unrescue  Restore server from rescue mode

## Delete

openstack server delete => terminate instance 
~~~
openstack server delete ubuntu14
~~~
## Lock/Unlock
~~~
$ openstack server lock ubuntu14
$ openstack server delete ubuntu14
Instance c8a959e9-e45e-486b-aa75-16c062ed54be is locked (HTTP 409) (Request-ID: req-dc913ea6-8f9d-4c2d-aed5-2951bc84545f)
$ #unlock
$ openstack server unlock ubuntu14
~~~

## Migrate

~~~
# openstack server migrate -h
usage: openstack server migrate [-h] [--live <hostname>]
                                [--shared-migration | --block-migration]
                                [--disk-overcommit | --no-disk-overcommit]
                                [--wait]
                                <server>

Migrate server to different host

positional arguments:
  <server>              Server (name or ID)

optional arguments:
  -h, --help            show this help message and exit
  --live <hostname>     Target hostname
  --shared-migration    Perform a shared live migration (default)
  --block-migration     Perform a block live migration
  --disk-overcommit     Allow disk over-commit on the destination host
  --no-disk-overcommit  Do not over-commit disk on the destination host
                        (default)
  --wait                Wait for resize to complete
~~~

~~~
$ openstack server migrate jb16 --wait --disk-overcommit
~~~

## SSH
~~~
$ openstack server ssh --login ubuntu --identity cliconfig/stackato.pem jb16
 openstack server ssh --login ubuntu --identity cliconfig/stackato.pem jb16
The authenticity of host '172.16.108.37 (172.16.108.37)' can't be established.
ECDSA key fingerprint is 13:51:ea:34:9c:66:f8:5b:9e:5b:1b:d3:d0:95:56:b1.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '172.16.108.37' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04 LTS (GNU/Linux 4.4.0-21-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

153 packages can be updated.
49 updates are security updates.


Last login: Thu Nov 17 13:43:13 2016 from 10.8.2.105
ubuntu@jb16:~$ 
~~~


## Pause

~~~
$ openstack server list
+--------------------------------------+-----------------------------------------+--------+---------------------------------+---------------------+
| ID                                   | Name                                    | Status | Networks                        | Image Name          |
+--------------------------------------+-----------------------------------------+--------+---------------------------------+---------------------+
| 0d58ead7-107f-426b-ae8a-e7d7d3867311 | coreos                                  | ACTIVE | priv=10.0.0.24, 172.16.108.38   | coreos-1185.3.0     |
| 100752b7-bfa2-4c32-b1da-36a42f674629 | jb16                                    | ACTIVE | priv=10.0.0.6, 172.16.108.37    | Ubuntu-16.04-x86_64 |
| 7c965152-8960-49f6-b50a-dc843a6e189a | jumpbox                                 | ACTIVE | priv=10.0.0.5, 172.16.108.36    | Ubuntu-14.04-x86_64 |
+--------------------------------------+-----------------------------------------+--------+---------------------------------+---------------------+

$ openstack server pause jb16
$ openstack server list
+--------------------------------------+-----------------------------------------+--------+---------------------------------+---------------------+
| ID                                   | Name                                    | Status | Networks                        | Image Name          |
+--------------------------------------+-----------------------------------------+--------+---------------------------------+---------------------+
| 0d58ead7-107f-426b-ae8a-e7d7d3867311 | coreos                                  | ACTIVE | priv=10.0.0.24, 172.16.108.38   | coreos-1185.3.0     |
| 100752b7-bfa2-4c32-b1da-36a42f674629 | jb16                                    | PAUSED | priv=10.0.0.6, 172.16.108.37    | Ubuntu-16.04-x86_64 |
| 7c965152-8960-49f6-b50a-dc843a6e189a | jumpbox                                 | ACTIVE | priv=10.0.0.5, 172.16.108.36    | Ubuntu-14.04-x86_64 |
+--------------------------------------+-----------------------------------------+--------+---------------------------------+---------------------+

$ openstack server unpause jb16

$ openstack server list
+--------------------------------------+-----------------------------------------+--------+---------------------------------+---------------------+
| ID                                   | Name                                    | Status | Networks                        | Image Name          |
+--------------------------------------+-----------------------------------------+--------+---------------------------------+---------------------+
| 0d58ead7-107f-426b-ae8a-e7d7d3867311 | coreos                                  | ACTIVE | priv=10.0.0.24, 172.16.108.38   | coreos-1185.3.0     |
| 100752b7-bfa2-4c32-b1da-36a42f674629 | jb16                                    | ACTIVE | priv=10.0.0.6, 172.16.108.37    | Ubuntu-16.04-x86_64 |
| 7c965152-8960-49f6-b50a-dc843a6e189a | jumpbox                                 | ACTIVE | priv=10.0.0.5, 172.16.108.36    | Ubuntu-14.04-x86_64 |
+--------------------------------------+-----------------------------------------+--------+---------------------------------+---------------------+

~~~

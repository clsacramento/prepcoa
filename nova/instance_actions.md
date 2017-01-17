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

openstack server delete => terminate instance (on horizon dashboard)


## Lock/Unlock
~~~
$ openstack server lock ubuntu14
$ openstack server delete ubuntu14
Instance c8a959e9-e45e-486b-aa75-16c062ed54be is locked (HTTP 409) (Request-ID: req-dc913ea6-8f9d-4c2d-aed5-2951bc84545f)

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

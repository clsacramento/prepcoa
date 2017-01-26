# Create Volumes

pvcreate /dev/sdb
vgcreate cinder-volumes /dev/sdb

vim /etc/lvm/lvm.conf
...
  devices
  ...
    scan = [ "/dev" ]
    ...
    filter = [ "/a/sdb/", "r/.*/" ]

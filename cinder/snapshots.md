# Volume snapshots

Before taking snapshots a volume has to detached from the instance otherwise you have to pass --force True when creating the snapshot.

cinder snapshot-create --display-name snap1 vol_id
  or
cinder snapshot-create --force True --display-name snap1 vol_id_that_is_attached

cinder snapshot-list

cinder snapshot-delete snapshot_id




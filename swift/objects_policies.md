# Storage policies

## Changing an object's ACL

Adding a read ACL

### To a container
~~~
$ swift post test_container -r ".r:*"
~~~

### To a file
~~~
swift post test_container .bashrc -r ".r:*"
~~~

### Write ACL
~~~
swift post test_container .bashrc -w ".r:*"
~~~

### Syntax:
 - ".r:*" : All referrers.
 - ".r:example.com,swift.example.com" : Comma separated list of referrers
 - ".rlistings" : Container listing access
 - "AUTH_<username>" : Access for a particular OpenStack Auth user

### Removing ACLs:
~~~
swift post test_container .bashrc -r ""
~~~
~~~
swift post test_container .bashrc -w ""
~~~

## Expiring Objects
Object deletion is set on epoch time. For example, one day would be:
~~~
24*60*60 = 846400
~~~

Generating an epoch
~~~
date --date="19-FEB-17 14:30:15" +%s
1487514615

date -d@1487514615
Sun Feb 19 14:30:15 UTC 2017
~~~

~~~
$ swift post test_container .bashrc -H "X-Delete-At:1513641600"

$ swift post test_container .bashrc -H "X-Delete-After:846400"

$ swift stat test_container .bashrc
       Account: AUTH_5f635ca39e7044e9adce0496a0ed8daa
     Container: test_container
        Object: .bashrc
  Content Type: application/octet-stream
Content Length: 176
 Last Modified: Wed, 17 May 2017 11:01:23 GMT
          ETag: c36f10fd0ff59c3bcce088d7a7a6c410
   X-Delete-At: 1495865282
 Accept-Ranges: bytes
   X-Timestamp: 1495018882.33946
    X-Trans-Id: tx1d4e31b47c624be2816bf-00591c2d85

$ swift post test_container .bashrc -H "X-Remove-Delete-At:"
~~~

Dates must be in the future:
~~~
$ swift post test_container .bashrc -H "X-Delete-At:1487514615"
Object POST failed: http://162.242.235.96:8080/v1/AUTH_5f635ca39e7044e9adce0496a0ed8daa/test_container/.bashrc 400 Bad Request   X-Delete-At in past
~~~


If the object gets deleted, we get:
~~~
$ swift stat test_container .bashrc
Object HEAD failed: http://162.242.235.96:8080/v1/AUTH_5f635ca39e7044e9adce0496a0ed8daa/test_container/.bashrc 404 Not Found
~~~


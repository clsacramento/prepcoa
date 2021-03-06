# Storage policies

## Changing an object's ACL

Doc: https://docs.openstack.org/swift/ocata/overview_acl.html


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
swift post test_container .bashrc -w "*:*"
~~~

### Syntax for referers (only for read ACLs):
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

## More ACL's examples

### Create a public container

Pre-create some files to upload
~~~
# mkdir public
# cd public
echo a > a
echo b > b
# ls
a  b
~~~

Upload these files:
~~~
# swift upload public a 
a
# swift upload public b
b
~~~

### Make the container public:
The following allows anybody to list objects in the 'public' container and download objects. The users do not need to include a token in their request. This ACL is commonly referred to as making the container “public”. It is useful when used with StaticWeb:

~~~
# swift post public --read-acl ".r:*,.rlistings
~~~


List information about the uploaded object
~~~
# swift stat public a -v
           URL: https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd/public/a
    Auth Token: 845e87faac8f44de8294ff5d3e5f6ed7
       Account: AUTH_2fa0fe127fdb40f68cdefde64e36c7fd
     Container: public
        Object: a
  Content Type: application/octet-stream
Content Length: 2
 Last Modified: Wed, 27 Sep 2017 11:10:17 GMT
          ETag: 60b725f10c9c85c70d97880dfe8191b3
    Meta Mtime: 1506510570.598324
 Accept-Ranges: bytes
   X-Timestamp: 1506510616.78628
    X-Trans-Id: tx93bbad893bba457e8021c-0059cb89f6
~~~

Notice the container URL. It can be used to download the objects. Anyone who knows the URL can download it:
~~~
curl -k https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd/public/a
a
curl -k https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd/public/b
b
~~~

### Make a container publicly writable (but requires keystone token):
The following allows anybody to upload or download objects. However, to download an object, the exact name of the object must be known since users cannot list the objects in the container. The users must include a Keystone token in the upload request. However, it does not need to be scoped to the project associated with the container:


Upload files to a new 'publicrw' container
~~~
# swift upload publicrw a
a
# swift upload publicrw b
b
~~~

Apply the policy:

~~~
swift post publicrw --read-acl ".r:*" --write-acl "*:*"
~~~

Compare the differences between the public and the publirw containers:
~~~
# swift stat public -v
             URL: https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd/public
      Auth Token: e2da87b4937c4a089708b790f93bf043
         Account: AUTH_2fa0fe127fdb40f68cdefde64e36c7fd
       Container: public
         Objects: 2
           Bytes: 4
        Read ACL: .r:*,.rlistings
       Write ACL:
         Sync To:
        Sync Key:
   Accept-Ranges: bytes
X-Storage-Policy: General
   Last-Modified: Wed, 27 Sep 2017 11:17:33 GMT
     X-Timestamp: 1506510615.60352
    Content-Type: text/plain; charset=utf-8
      X-Trans-Id: txe8e792f880d34b03b904d-0059cb90d2
# swift stat publicrw -v
             URL: https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd/publicrw
      Auth Token: f7a9378cb15c495e82a5f72f3f030f5c
         Account: AUTH_2fa0fe127fdb40f68cdefde64e36c7fd
       Container: publicrw
         Objects: 2
           Bytes: 4
        Read ACL: .r:*
       Write ACL: *:*
         Sync To:
        Sync Key:
   Accept-Ranges: bytes
X-Storage-Policy: General
   Last-Modified: Wed, 27 Sep 2017 11:51:19 GMT
     X-Timestamp: 1506512993.38670
    Content-Type: text/plain; charset=utf-8
      X-Trans-Id: tx66efad0f53044ca9b894b-0059cb90d6
~~~

#### Login as different user and observe differences in both containers.

List is allowed on 'public' but not on 'publicrw':
~~~
# . user2rc #a user in a different tenant
# swift --os-storage-url https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd list public
a
b
# swift --os-storage-url https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd list publicrw
Container GET failed: https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd/publicrw?format=json 403 Forbidden  [first 60 chars of response] b'<html><h1>Forbidden</h1><p>Access was denied to this resourc'
Failed Transaction ID: tx2df09e3ae3f3452f80ed5-0059cba521
~~~
 
 Try uploading a file to each container:
~~~
# echo c > c
# swift --os-storage-url https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd upload public c
Warning: failed to create container 'public': 403 Forbidden: <html><h1>Forbidden</h1><p>Access was denied to this resourc
Object PUT failed: https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd/public/c 403 Forbidden  [first 60 chars of response] b'<html><h1>Forbidden</h1><p>Access was denied to this resourc'
# swift --os-storage-url https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd upload publicrw c
Warning: failed to create container 'publicrw': 403 Forbidden: <html><h1>Forbidden</h1><p>Access was denied to this resourc
c
# curl -k https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd/publicrw/c
c
~~~
 Despite the Warnings about not being able to create a container, the 'c' file is uploaded to 'publicrw' but not to 'public'.
 
 #### TIP
 
 You can obtain the token of an authenticated user with 'swift auth' and then use cURL to manage objects:
~~~
# source user2rc
# swift auth
export OS_STORAGE_URL=https://172.16.101.5:8080/v1/AUTH_825bbe64b5fe4a5b93117e996773f4da
export OS_AUTH_TOKEN=bff82feb88534af5b160e313036ffd49
# curl -k -X GET -H 'X-Auth-Token: 34dce60c28fa42e498ce3bed4b1f0fd2' https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd/public # list objects
a
b
# echo d > d #new file
# curl -k -X PUT -H 'X-Auth-Token: 34dce60c28fa42e498ce3bed4b1f0fd2' https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd/publicrw/ -T d
# curl -k -X GET -H 'X-Auth-Token: 34dce60c28fa42e498ce3bed4b1f0fd2' https://172.16.101.5:8080/v1/AUTH_2fa0fe127fdb40f68cdefde64e36c7fd/publicrw/d # download object
d
~~~

## Expiring Objects

Doc: https://docs.openstack.org/swift/ocata/overview_expiring_objects.html

### Epoch
Object deletion is set on epoch time. For example, one day (846400 seconds) would be:
~~~
# echo $((24*60*60))
86400
~~~

### Generating an epoch
~~~
date --date="19-FEB-17 14:30:15" +%s
1487514615

date -d@1487514615
Sun Feb 19 14:30:15 UTC 2017
~~~
TIP: 'date --help' will give an example of epoch conversion

Obtaining date in a format that can be converted to epoch:
~~~
# date +"%d-%b-%Y %T"
27-Sep-2017 14:35:13
# date -d"27-Sep-2017 14:33:10" +%s
1506522790
# date  -d@1506522790
Wed Sep 27 14:33:10 UTC 2017
# date  -d@1506522790 +"%d-%b-%Y %T"
27-Sep-2017 14:33:10
~~~

### Configuring object expiration
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


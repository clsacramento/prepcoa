# Object storage  - Swift


## Create object

This creates an object on a new container called 'myfiles' and verifies the new object is there.

~~~
# swift list
# swift upload myfiles demorc 
demorc
# swift list
myfiles
# swift list myfiles
demorc
# swift stat -v myfiles demorc
           URL: http://146.20.110.250:8080/v1/AUTH_4d84c8669cc149bea13b551cd872e664/myfiles/demorc
    Auth Token: 1c111d91c3e74bc49ec263bcfab8ebc8
       Account: AUTH_4d84c8669cc149bea13b551cd872e664
     Container: myfiles
        Object: demorc
  Content Type: application/octet-stream
Content Length: 747
 Last Modified: Fri, 20 Jan 2017 12:34:47 GMT
          ETag: ebbf0d613105ecbfa4afda809f78798c
    Meta Mtime: 1484915610.729789
 Accept-Ranges: bytes
   X-Timestamp: 1484915686.86336
    X-Trans-Id: tx9ec8e8115cec432cbf9e1-0058820415

~~~

## Accessing the object
By default the object is not public.
~~~
curl http://146.20.110.250:8080/v1/AUTH_4d84c8669cc149bea13b551cd872e664/myfiles/demorc
<html><h1>Unauthorized</h1><p>This server could not verify that you are authorized to access the document you requested.</p></html>
~~~
But it can be accessed by passing the Auth Token provided by 'swift stat' command to the header of the request:

~~~
# curl -X GET -H 'X-Auth-Token: 1c111d91c3e74bc49ec263bcfab8ebc8' http://146.20.110.250:8080/v1/AUTH_4d84c8669cc149bea13b551cd872e664/myfiles/demorc
export OS_AUTH_URL=...
~~~
A Swift ACL can be added to make it publicly readable:
~~~
# swift post myfiles -r ".r:*"
# curl http://146.20.110.250:8080/v1/AUTH_4d84c8669cc149bea13b551cd872e664/myfiles/demorcexport 
OS_AUTH_URL=...
~~~

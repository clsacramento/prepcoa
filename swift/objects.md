# Object storage  - Swift

## Create an empty container

~~~
$ swift post test_container
~~~

## Upload an object

If the container does not exist, it will be created
~~~
$ swift upload test_container ./.bashrc 
.bashrc
~~~

## Listing objects and containers

~~~
$ swift list 
plop
test_container

$ swift list test_container
.bashrc

$ swift stat test_container .bashrc -v
           URL: http://162.242.235.96:8080/v1/AUTH_5f635ca39e7044e9adce0496a0ed8daa/test_container/.bashrc
    Auth Token: 5e9cebc81a654c14a80f4e3e1986c255
       Account: AUTH_5f635ca39e7044e9adce0496a0ed8daa
     Container: test_container
        Object: .bashrc
  Content Type: application/octet-stream
Content Length: 176
 Last Modified: Wed, 17 May 2017 10:26:11 GMT
          ETag: c36f10fd0ff59c3bcce088d7a7a6c410
    Meta Mtime: 1388283991.000000
 Accept-Ranges: bytes
   X-Timestamp: 1495016770.22870
    X-Trans-Id: tx15ead3026de540128bd96-00591c25fe
~~~

## Downloading an object

~~~
$ swift download test_container .bashrc
.bashrc [auth 0.354s, headers 0.471s, total 0.472s, 0.001 MB/s]

$ swift download test_container .bashrc -o /tmp/test_file
.bashrc [auth 0.350s, headers 0.457s, total 0.457s, 0.002 MB/s]
$ ls -lah /tmp/test_file
-rw-r--r-- 1 root root 176 Dec 29  2013 /tmp/test_file


~~~

## Accessing the object
By default the object is not public.
~~~
curl http://146.20.110.250:8080/v1/AUTH_4d84c8669cc149bea13b551cd872e664/myfiles/demorc
<html><h1>Unauthorized</h1><p>This server could not verify that you are authorized to access the document you requested.</p></html>
~~~
But it can be accessed by passing the Auth Token provided by 'swift stat' command to the header of the request:

~~~
$ curl -X GET -H 'X-Auth-Token: 1c111d91c3e74bc49ec263bcfab8ebc8' http://146.20.110.250:8080/v1/AUTH_4d84c8669cc149bea13b551cd872e664/myfiles/demorc
export OS_AUTH_URL=...
~~~


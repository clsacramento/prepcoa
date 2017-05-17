# Operation

## Request a token with CuRL

~~~
$ curl -d '{"auth":{"passwordCredentials":{"username": "admin", "password": "openstack"},"tenantName": "admin"}}' -H "Content-Type: application/json" http://localhost:5000/v2.0/tokens | python -m json.tool
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  4906  100  4805  100   101  56554   1188 --:--:-- --:--:-- --:--:-- 57202

[...]
        "token": {
            "audit_ids": [
                "UZZr3BpdRFK0037sdco1Zw"
            ],
            "expires": "2017-05-17T10:50:49Z",
            "id": "38b54ea7a36145ba92bb02faafb3689e",
            "issued_at": "2017-05-17T09:50:49.529886",
            "tenant": {
                "description": "admin tenant",
                "enabled": true,
                "id": "5f635ca39e7044e9adce0496a0ed8daa",
                "name": "admin"
            }
[...]
~~~

## Request a token with OpenStack client

~~~
$ openstack token issue
+------------+----------------------------------+
| Field      | Value                            |
+------------+----------------------------------+
| expires    | 2017-05-17T10:53:34Z             |
| id         | 208fa894b1eb4c15a717a170ab58a2a1 |
| project_id | 5f635ca39e7044e9adce0496a0ed8daa |
| user_id    | 9a2d4652e47845bca9fc575b85996d68 |
+------------+----------------------------------+
~~~

## Make a request to an API with CuRL
For example listing servers in a tenant

~~~
$ curl -H "X-Auth-Token: 54219fa19b1a4bc680e44adaa664ab5e" http://localhost:8774/v2/5f635ca39e7044e9adce0496a0ed8daa/servers
{"servers": []}
~~~


## Verify that Keystone can generate tokens
~~~
$ openstack --os-auth-url http://127.0.0.1:5000/v2.0 token issue
+------------+----------------------------------+
| Field      | Value                            |
+------------+----------------------------------+
| expires    | 2017-05-17T11:07:09Z             |
| id         | 180aa1aafdce48e787e6263c3d73614c |
| project_id | 5f635ca39e7044e9adce0496a0ed8daa |
| user_id    | 9a2d4652e47845bca9fc575b85996d68 |
+------------+----------------------------------+
$ openstack --os-auth-url http://127.0.0.1:35357/v2.0 token issue
+------------+----------------------------------+
| Field      | Value                            |
+------------+----------------------------------+
| expires    | 2017-05-17T11:07:18Z             |
| id         | b02df4e3ee2340d2baf0ad547b4a06ce |
| project_id | 5f635ca39e7044e9adce0496a0ed8daa |
| user_id    | 9a2d4652e47845bca9fc575b85996d68 |
+------------+----------------------------------+
~~~

## Check Keystone logs
~~~
$ cat /var/log/keystone/keystone.log | grep ERROR
~~~

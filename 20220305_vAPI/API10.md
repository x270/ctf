## API10  Insufficient Logging & Monitoring

OWASP Top 10 2017でも物議をかもしたと思うが、  
外部からの診断の観点では「不十分なロギングとモニタリング」を見つけるのは困難を難しい。  
vAPIでもどうやら問題を作る気はないようだ。
> Nothing has been logged or monitored , You caught us :( !

```http
GET /vapi/api10/user/flag HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.29.0
Accept: */*
Postman-Token: 9251aa8f-ae90-4e79-8c63-9b0194b2e8b7
Host: localhost
Accept-Encoding: gzip, deflate
Connection: close
Cookie: PHPSESSID=1d146ce6c7cda9305cf082f33a671aeb

```
```http
HTTP/1.1 200 OK
Host: localhost
Date: Sat, 05 Mar 2022 14:51:47 GMT
Connection: close
X-Powered-By: PHP/7.4.7
Cache-Control: no-cache, private
Date: Sat, 05 Mar 2022 14:51:47 GMT
Content-Type: application/json

{"message":"Hey! I didn't log and monitor all the requests you've been sending. That's on me...","flag":"flag{api10_5db611f7c1ffd747971f}"}
```

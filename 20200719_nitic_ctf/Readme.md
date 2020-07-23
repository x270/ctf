# nitic_ctf Writeup
- 2020/7/19(日)開催
- 茨城高専の有志による初心者向けCTFの第1回。第2回にも期待。  
https://wakuwakuclub.connpass.com/event/182469/
- Pwn2問を残し、1200点で4位。なお、同得点超多数。
- 解けた順にWriteup。最適解というわけではありません。

## anim [Forensic]
拡張子の無いファイル「flag」が与えられる。  
まずは何のファイルか確認。
```bash
$ file flag  
flag: Microsoft PowerPoint 2007+
```

zip形式で展開すると、フラグの書かれた画像ファイルが見つかる。
```bash
.flag/ppt/media/image1.png  
```
> nitic_ctf{ppppptx}

## Fortran [Reversing]
実行ファイル「problem」と「problem.exe」が与えられる。  
どうすればよいか考える前に、とりあえずstringsコマンド。
```
$ strings problem | grep ctf
replacenitictf{Fortran}
$ strings problem.exe | grep ctf
nitic_ctf{No_FORTRAN_Yes_Fortran}
```
違うフラグが返却されるが、exeの方が正解。  
終了後の投稿によるとelfの方はファイルが古かったとのこと。  
> nitic_ctf{No_FORTRAN_Yes_Fortran}

## cha1n [Misc]
「bat」と「sh」が与えられる。  
「bat」が1つ多いのは誤りとのこと。回答する上では問題なし。
```
$ ls
1.bat  1.sh  a.bat  a.sh  c.bat  c.sh  d.bat  h.bat  h.sh  n.bat  n.sh
```

1.shを見る。何やら置換しているが、もともとの値が不明。
```bash:1.sh
str=$(cat -)
str=${str//s/a}
str=${str//x/c}
echo ${str//qq/rr}
```

順に開いていくとc.shにもともとの値が書かれている。
```bash:c.sh
str="njtjxpxtfwx()s1Kpx()s1Kpx()s1Kpx()s1Kpx()s1Kp5x8mb83"
str=${str//()/h}
str=${str//p/_}
echo ${str//w/oo}
```

問題タイトル「cha1n」の順に実行すればよいと分かる。
```
$ ./c.sh | ./h.sh | ./a.sh  | ./1.sh | ./n.sh
nitic_ctf{cha1n_cha1n_cha1n_cha1n_cha1n_5combo}
```
> nitic_ctf{cha1n_cha1n_cha1n_cha1n_cha1n_5combo}

## 8^2 [Web]
文字列が与えられる。
```
data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD（長いので省略）
```

dataスキーマで画像ファイルであることが分かるので、imgタグを付けて、拡張子をhtmlで保存し、ブラウザで開く。
```html:web.html
<img src="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD（長いので省略）">
```
> nitic_ctf{nemu_nemu_panti_shitai}

## prime_factorization [PPC]
ある数値を素因数分解した結果を、指定された形で回答する。  
PPC問なので、自力で素因数分解するコードを書くべきかもしれないが、普通のコマンドで実行。
```
$ factor 408410100000
408410100000: 2 2 2 2 2 3 3 3 3 3 5 5 5 5 5 7 7 7 7 7
```
2^5+3^5+5^5+7^5なので  
> nitic_ctf{2_5_3_5_5_5_7_5}

## shift_only [Crypto]
暗号化コードと結果が与えられる。
```Python:encrypt_flag.py
from os import environ
flag = environ["FLAG"]
format = environ["FORMAT"]

shift_table = "abcdefghijklmnopqrstuvwxyz0123456789{}_"
def encrypt(text: str, shift: int) -> str:
    assert  0 <= shift <= 9
    res = ""
    for c in text:
        res += shift_table[(shift_table.index(c)+shift)%len(shift_table)]
    return str(shift) + res
for shift in format:
    flag = encrypt(flag, int(shift))
with open("encrypted.flag", "w") as f:
    f.write(flag)
```
```text:encrypted.flag
6}bceijnob9h9303h6yg896h0g896h0g896h01b40g896hz
```

元の文字列をshift_tableと照らして数文字ずらし、ずらした数を先頭に付けて…を繰り返している。  
与えられたフラグの先頭の数字がずらした数なので、その文字数だけ逆方向に戻し、先頭が数字じゃなくなるまで繰り返す。
```Python:dec.py
shift_table = 'abcdefghijklmnopqrstuvwxyz0123456789{}_'
def decrypt(text, shift):
    res = ""
    for c in text:
        res += shift_table[(shift_table.index(c)-shift)]
    return res

flag = '6}bceijnob9h9303h6yg896h0g896h0g896h01b40g896hz'
while flag[0].isdigit():
    flag = (decrypt(flag[1:], int(flag[0])))
    print(flag)
```
```bash
$ python dec.py
589}cdhi83b3xuxb0sa230bua230bua230buv8yua230bt
346{}cd3y9ysps9vn8xyv9p8xyv9p8xyv9pq3tp8xyv9o
1378_a0v6vpmp6sk5uvs6m5uvs6m5uvs6mn0qm5uvs6l
267}_zu5uolo5rj4tur5l4tur5l4tur5lmzpl4tur5k
459{xs3smjm3ph2rsp3j2rsp3j2rsp3jkxnj2rsp3i
156tozoifizldynolzfynolzfynolzfgtjfynolze
45snynhehykcxmnkyexmnkyexmnkyefsiexmnkyd
1ojujdadug}tijguatijguatijguaboeatijgu_
nitic_ctf{shift_shift_shift_and_shift}
```
> nitic_ctf{shift_shift_shift_and_shift}

## Dangerous Twitter [Recon] 
作問者のTwitterで投稿されたパスワードを探す。  
hogehogeと書かれたつぶやきを見つけたがハズレ。

投稿された写真の中に、パスワードを書いたメモ用紙が写りこんでいた。
> nitic_ctf{abaE33F5}

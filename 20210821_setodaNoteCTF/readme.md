# setodanoteCTF
2021.8.21 PM 9:00 ～ 2021.9.4 PM 9:00 (JST)と長めの期間で初心者向けの問題も多いCTF。  
普段はまったく手も足も出ないジャンルが調べながらとはいえ解けて楽しかった。  
100位に届かなかったのが残念。    
![image](https://user-images.githubusercontent.com/42441861/132118448-6b03f797-73a4-4bf6-9982-2fc10198e315.png)

## Misc 

### Welcome
Webページへアクセス。  
IDとpasswordを入れて、ファイルをtailで開けば正解。

### morse_one
与えられた文字列を解読。
```
DDDBSDDSBDDDSDBDSBBBSDBBDSDBDDSDSBDDB
```

問題名からモール信号のようなので、Dをドット、Bをバー、Sをスペースに置換。
```
...- .. -... .-. --- .--. .-.. . -..-
```

CyberChedで解読して正解。
https://gchq.github.io/CyberChef/#recipe=From_Morse_Code('Space','Line%20feed')&input=Li4uLSAuLiAtLi4uIC4tLiAtLS0gLi0tLiAuLS4uIC4gLS4uLQ

### Hash
90個あるファイルから、特定のハッシュ値を持つものを見つけたい。  
問題文より、SHA256のハッシュが必要そう。

https://expnote.com/windows-hash-check-batch-file/
上記のブログのバッチを使用させていただきました。
```
@echo off
:loop
if "%~1" == "" goto end
certutil -hashfile %1 SHA256 >> result.txt
shift
goto loop
:end
pause
```
24,34,79のファイルが該当するので、中身の文字列を結合して終わり。

### F
特徴的な、記号だけで構成された言語。Brainf*ck。
```Brainfuck
+++++++[->++++++++++>+++++++++++>+++++++++>++++++++++<<<<]>.>-.>++.>+.>++++++++[>++++<-]>.>+++++[->+++++++++++++++++++++>+++++++++++++++++++++++<<]>.>.>++++++++[>++++<-]>.>++++++++++[->++++++++++>++++++++++>++++++++++>++++++++++>++++++++++++<<<<<]>++.>++++++++.>---.>+++.>+++.>++++++++[>++++++++<-]>++++.>+++++++++++[>++++++++++>++++++++++<<-]>+.>.>++++[>++++++++++<-]>-.>+++[->+++++[->++++++++<]<]>>----.>++++++++++[->++++++++++>+++++++++>++++++++++++>++++++++++>+++++++++<<<<<]>-----.>-----.>-----.>+.>+++++.>++++++++++++++++++++[>++++++>+++++>+++++>+++++>+++>++<<<<<<-]>----.>++++.>+.>-----.>++++++++++.>+++++.>++++++++++[->+++++++++>+++++++++++>+++++++++++>++++++++++<<<<]>---.>+.>++++.>.>+++[->+++++++++++<]>..>+++++[->+++++[->+++++<]<]>>.
```
```shell
$ sudo apt install bf
$ bf ctf.bf
flag{Don't_Use_the_F-Word!!}
```

### magic_number
マジックナンバーを確認するため、XXDコマンドで先頭を見る。  
設問に合うものを見つけて終わり。
```shell
# xxd post.png | head -n 1
00000000: 8950 4e47 0d0a 1a0a 0000 000d 4948 4452  .PNG........IHDR
# xxd rar.rar | head -n 1
00000000: 5261 7221 1a07 0100 3392 b5e5 0a01 0506  Rar!....3.......
# xxd light.jpg | head -n 1
00000000: ffd8 ffe0 0010 4a46 4946 0001 0101 0060  ......JFIF.....`
```

### Stegano
中央にうっすらと文字が見えているが、それ以外にも隠れている。  
「うさみみハリケーン」の「青い空を見上げればいつもそこに白い猫」を使用。  
ステガノグラフィー解析機能で、「赤色ビット4抽出」「青色ビット4抽出」し、  
中央の文字と連結して回答。




### morse_zero
メモ帳などで開くと分からないが、Zの間に「U+200B」や「U+200C」が混じっている。
```
‌‌​​Z​Z​‌​Z‌‌‌‌‌Z​​‌‌​‌Z​‌‌Z​‌‌‌‌Z‌​​Z‌Z​​​​Z​​‌‌​‌Z​​​Z​‌‌​Z​‌Z‌​‌​Z​
```

文字種の数から、またモールス記号かなと推察。  
どれがどの記号か分からないが、あたりを付けて置換してみる。
```
--.. . .-. ----- ..--.- .-- .---- -.. - .... ..--.- ... .--. .- -.-. .
```

https://gchq.github.io/CyberChef/#recipe=From_Morse_Code('Space','Line%20feed')&input=LS0uLiAuIC4tLiAtLS0tLSAuLi0tLi0gLi0tIC4tLS0tIC0uLiAtIC4uLi4gLi4tLS4tIC4uLiAuLS0uIC4tIC0uLS4gLg

### ransom_note
GandCrabによる暗号化されてしまったファイルを戻す。  

こんな記事を見つけたので、ツールを使用して復号。
> ランサムウェア「GandCrab」対応の復号ツール公開、複数バージョンに対応 
> https://japan.zdnet.com/article/35127633/

### i_knew_it
RC4かな？と思い、入れてみたら正解。

### Redacted
PDFの文章の一部が黒塗りになっている。  
テキストではなく画像のようで、文章としてのコピペができない。  

Microsoft Wordで開くと、黒塗りオブジェクトを移動できるので、  
出てきたフラグが答え。

## Crypto

### base64
```shell
$ echo "ZmxhZ3tJdCdzX2NhbGxlZF9iYXNlNjQhfQ==" | base64 -d
flag{It's_called_base64!}[x270@X270]
```
### ROT13
ROT13で「synt{Rira_lbh_Oehghf?」を戻して終わり。  
https://gchq.github.io/CyberChef/#recipe=ROT13(true,true,false,13)&input=c3ludHtSaXJhX2xiaF9PZWhnaGY/fQ

### pui_pui
From Hexで戻して終わり。
```
\x41\x3a\x44\x6f\x20\x79\x6f\x75\x20\x6b\x6e\x6f\x77\x20\x4d\x6f\x6c\x63\x61\x72\x3f\x0a\x0a\x42\x3a\x4f\x66\x20\x63\x6f\x75\x72\x73\x65\x21\x20\x49\x20\x6c\x6f\x76\x65\x20\x74\x68\x65\x20\x73\x63\x65\x6e\x65\x20\x77\x68\x65\x72\x65\x20\x68\x65\x20\x73\x69\x6e\x6b\x73\x20\x69\x6e\x74\x6f\x20\x74\x68\x65\x20\x62\x6c\x61\x73\x74\x20\x66\x75\x72\x6e\x61\x63\x65\x20\x77\x68\x69\x6c\x65\x20\x67\x69\x76\x69\x6e\x67\x20\x74\x68\x65\x20\x74\x68\x75\x6d\x62\x73\x20\x75\x70\x2e\x0a\x0a\x41\x3a\x2e\x2e\x2e\x20\x57\x68\x61\x74\x3f\x0a\x0a\x42\x3a\x62\x74\x77\x2c\x20\x74\x68\x65\x20\x66\x6c\x61\x67\x20\x69\x73\x20\x66\x6c\x61\x67\x7b\x48\x61\x76\x65\x5f\x79\x6f\x75\x5f\x65\x76\x65\x72\x5f\x68\x65\x61\x72\x64\x5f\x6f\x66\x5f\x48\x65\x78\x64\x75\x6d\x70\x3f\x7d\x2e\x0a
```
https://gchq.github.io/CyberChef/#recipe=From_Hex('Auto')&input=XHg0MVx4M2FceDQ0XHg2Zlx4MjBceDc5XHg2Zlx4NzVceDIwXHg2Ylx4NmVceDZmXHg3N1x4MjBceDRkXHg2Zlx4NmNceDYzXHg2MVx4NzJceDNmXHgwYVx4MGFceDQyXHgzYVx4NGZceDY2XHgyMFx4NjNceDZmXHg3NVx4NzJceDczXHg2NVx4MjFceDIwXHg0OVx4MjBceDZjXHg2Zlx4NzZceDY1XHgyMFx4NzRceDY4XHg2NVx4MjBceDczXHg2M1x4NjVceDZlXHg2NVx4MjBceDc3XHg2OFx4NjVceDcyXHg2NVx4MjBceDY4XHg2NVx4MjBceDczXHg2OVx4NmVceDZiXHg3M1x4MjBceDY5XHg2ZVx4NzRceDZmXHgyMFx4NzRceDY4XHg2NVx4MjBceDYyXHg2Y1x4NjFceDczXHg3NFx4MjBceDY2XHg3NVx4NzJceDZlXHg2MVx4NjNceDY1XHgyMFx4NzdceDY4XHg2OVx4NmNceDY1XHgyMFx4NjdceDY5XHg3Nlx4NjlceDZlXHg2N1x4MjBceDc0XHg2OFx4NjVceDIwXHg3NFx4NjhceDc1XHg2ZFx4NjJceDczXHgyMFx4NzVceDcwXHgyZVx4MGFceDBhXHg0MVx4M2FceDJlXHgyZVx4MmVceDIwXHg1N1x4NjhceDYxXHg3NFx4M2ZceDBhXHgwYVx4NDJceDNhXHg2Mlx4NzRceDc3XHgyY1x4MjBceDc0XHg2OFx4NjVceDIwXHg2Nlx4NmNceDYxXHg2N1x4MjBceDY5XHg3M1x4MjBceDY2XHg2Y1x4NjFceDY3XHg3Ylx4NDhceDYxXHg3Nlx4NjVceDVmXHg3OVx4NmZceDc1XHg1Zlx4NjVceDc2XHg2NVx4NzJceDVmXHg2OFx4NjVceDYxXHg3Mlx4NjRceDVmXHg2Zlx4NjZceDVmXHg0OFx4NjVceDc4XHg2NFx4NzVceDZkXHg3MFx4M2ZceDdkXHgyZVx4MGE

### tkys_secret_service
ROT13を1回実行。  
ところどころ読めるが、おかしい箇所がある。
priteftoiu → protection  
clag → flagなど、あたりをつけて換字を進める。  
```
The priteftoiu ic Ciutrilled Uuflazzocoed Iucirmatoiu (CUI) rezodeut ou uiucederal zyztemz aud irgauosatoiuz oz ic paraminut ompirtaufe ti cederal ageufoez aud fau doreftly ompaft the aboloty ic the cederal giverumeut ti znffezzcnlly fiudnft otz ezzeutoal mozzoiuz aud cnuftoiuz. Thoz pnblofatoiu privodez ageufoez woth refimmeuded zefnroty reqnoremeutz cir priteftoug the fiucodeutoaloty ic CUI wheu the oucirmatoiu oz rezodeut ou uiucederal zyztemz aud irgauosatoiuz; wheu the uiucederal irgauosatoiu oz uit filleftoug ir maoutaououg oucirmatoiu iu behalc ic a cederal ageufy ir nzoug ir iperatoug a zyztem iu behalc ic au ageufy; aud where there are ui zpefocof zacegnardoug reqnoremeutz cir priteftoug the fiucodeutoaloty ic Flag oz clag{pnopno_far_ic_mil} CUI prezfrobed by the anthirosoug law, regnlatoiu, ir giverumeutwode pilofy cir the CUI fategiry lozted ou the CUI Regoztry. The reqnoremeutz apply ti all fimpiueutz ic uiucederal zyztemz aud irgauosatoiuz that prifezz, ztire, aud/ir trauzmot CUI, ir that privode priteftoiu cir znfh fimpiueutz. The zefnroty reqnoremeutz are outeuded cir nze by cederal ageufoez ou fiutraftnal vehoflez ir ither agreemeutz eztablozhed betweeu thize ageufoez aud uiucederal irgauosatoiuz.
```
最後までできていないが、それっぽい単語のフラグが出てきた。これで正解。
```
The protectiou of foutrolled uuclazzified ouformatiou (fuo) rezideut iu uoufederal zyztemz aud orgauisatiouz iz of paramouut importauce to federal ageuciez aud cau directly impact the ability of the federal goverumeut to zuccezzfully couduct itz ezzeutial mizziouz aud fuuctiouz. Thiz publicatiou providez ageuciez with recommeuded zecurity requiremeutz for protectiug the coufideutiality of fuo wheu the iuformatiou iz rezideut iu uoufederal zyztemz aud orgauisatiouz; wheu the uoufederal orgauisatiou iz uot collectiug or maiutaiuiug iuformatiou ou behalf of a federal ageucy or uziug or operatiug a zyztem ou behalf of au ageucy; aud where there are uo zpecific zafeguardiug requiremeutz for protectiug the coufideutiality of clag iz flag{puipui_car_of_mol} fuo prezcribed by the authorisiug law, regulatiou, or goverumeutwide policy for the fuo category lizted iu the fuo Regiztry. The requiremeutz apply to all compoueutz of uoufederal zyztemz aud orgauisatiouz that procezz, ztore, aud/or trauzmit fuo, or that provide protectiou for zuch compoueutz. The zecurity requiremeutz are iuteuded for uze by federal ageuciez iu coutractual vehiclez or other agreemeutz eztablizhed betweeu thoze ageuciez aud uoufederal orgauisatiouz.
```

### lets_bake
CyberChefを使うことはすぐに分かったが、どうするのかよく分からない。
Input:
```
NzRmNDRiMWE0Y2M2ZGNiNzc3NTMyNTcwZjk0MTE4NTMyNTcxZjE1YTE1NTJkY2M0
```
Recipe:
```
RnJvbV9CYXNlNjQoJ0EtWmEtejAtOSsvPScsdHJ1ZSkN]b2[sRnJvbV9IZXgoJ05vbmUnKQ0=]b2[sRm9yaygnJScsJ18nLGZhbHNlKQ0=]b2[sUkM0KHsnb3B0aW9uJzonVVRGOCcsJ3N0cmluZyc6J2NoZWYnfSwnTGF0aW4xJywnTGF0aW4xJyk=
```
レシピの引数にしてもうまくいかない。```]b2[s```を除いてBase64デコードしてみる。
```
From_Base64('A-Za-z0-9+/=',true)
From_Hex('None')
Fork('%','_',false)
RC4({'option':'UTF8','string':'chef'},'Latin1','Latin1')
```

これをレシピに保存して、Inputを適用するとフラグが現れた。


## Rev
### Helloworld
`helloworld.exe`を渡される。  
exeなのでWindows環境でコマンドから実行してみる。
```command
C:\>helloworld.exe
Nice try, please set some word when you run me.
C:\>helloworld.exe hello
Good job, but please set 'flag' when you run me.
C:\>helloworld.exe flag
flag{free_fair_and_secure_cyberspace}
C:\>
```

### ELF
ファイル`elf`を渡される。  
名前から予測して、Linux環境で実行してみるが、実行できない。
```shell
# ./elf
-bash: ./elf: cannot execute binary file: Exec format error
```
マジックナンバーを確認する。頭が全部`X`になっていて、これはおかしい。
```
# xxd elf | head -n 1
00000000: 5858 5858 0201 0100 0000 0000 0000 0000  XXXX............
```
以下のように書き換えてあげると、実行でき、FLAGが表示される。
```
# xxd elf | head -n 1
00000000: 7f45 4c46 0201 0100 0000 0000 0000 0000  .ELF............
```

### Passcode
Linux環境で適当に実行してみる。
```
# ./passcode
Enter the passcode: X
Invalid passcode. Too short.
```
短いらしい。Stringsコマンドで拾える文字列を見る。  
以下は一部を省略している。8文字の数字を入れると、FLAGが現れる。
```shell
[root@X270]# strings passcode
Enter the passcode:
Invalid passcode.
Invalid passcode. Too short.
Invalid passcode. Too long.
20150109
The passcode has been verified.
Flag is : flag{%s}
Invalid passcode. Nice try.
```

## Forensics
### paint_flag
docxを渡される。開くと、flagが塗りつぶされた画像が出てくる。  
docxはzip圧縮形式なので、解凍。  
word\media\の下にflagの書かれた画像が置いてある。

### mail
Sent-1.emlをOutlookで開く。  
ファイルを添付した後が、base64された文字列で載っている。  

base64デコード結果をファイルで保存し、拡張子をzipにすると、中から画像が出てくる。 

### Timeline
ActivitiesCache.dbが渡される。
WindowsTimeline parserなるツールで中身を見れるらしい。  
https://github.com/kacos2000/WindowsTimeline/releases  
履歴に残っているテキストファイルの名前をすべてつなぐと答えになる。  

### tkys_another_day
いつも使用しているIrfanViewで、色情報が弄れないことに気づく。  
オプション→全フレームを抽出から、5フレームあることが分かる。  
5フレーム中の2つに、最初は見えていなかった文字が表示されるので、それを組み合わせて回答。


## Network
### Host
pcapを与えられるので、WireSharkで開く。  
TCPストリームに切り替えて、宛先のHost名を調べて終わり。

### tkys_never_die
pcapを与えられるので、WireSharkで開く。  
TCPストリーム2でpngを取得していることが分かる。  
ファイル→オブジェクトをエクスポート→HTTPで、image/pngの通信を指定し、saveして終わり。

### echo_request
ICMPの通信をよく見ると、1文字ずつ送信している。


### stay_in_touch
ストリーム14を見ると、なんだかあやしい文字列が入っている。  
Raw形式でSave as...より保存し、eml形式として開くと、  
パスワードが送付されている。

続けてストリーム12をよく見ると、
`Content-Type: application/x-zip-compressed;`ということで添付ファイルが付いていることが予測できる。  
同様にRaw形式でSave as...より保存し、eml形式として開くと、zipが添付されている。

先ほどのパスワードで解凍するとフラグを得られる。

### Digdig
`005aa002735f69735f44414d.setodanote.net`などの宛先があり、怪しい。  
全部抽出していく。
```
005aa002735f69735f44414d
005aa00663655f7472795f53
005aa0034d595f464c41477d
005aa0085f746861747d2066
005aa00a6c61677b444e535f
005aa00b5333637572313779
005aa0076f7272795f666f72
005aa00420666c6167206973
005aa0096c61672069732066
005aa00c5f5431303731217d
005aa011797d323232323232
005aa00d20666c6167206973
005aa00f335f6b33795f3135
005aa00e20666c61677b3768
005aa001666c61677b546869
005aa0105f35336375723137
005aa000666c616720697320
005aa00520666c61677b4e69
```
Cyber chefで`From Hex`出来そうだが、順番がおかしい。
先頭の`005aa002`のうち、後ろ二桁に重複がなく、並び替えができそう。
```
00666c616720697320
01666c61677b546869
02735f69735f44414d
034d595f464c41477d
0420666c6167206973
0520666c61677b4e69
0663655f7472795f53
076f7272795f666f72
085f746861747d2066
096c61672069732066
0a6c61677b444e535f
0b5333637572313779
0c5f5431303731217d
0d20666c6167206973
0e20666c61677b3768
0f335f6b33795f3135
105f35336375723137
11797d323232323232
```
並び替えたら、先頭二桁は要らないので削除。
```
666c616720697320
666c61677b546869
735f69735f44414d
4d595f464c41477d
20666c6167206973
20666c61677b4e69
63655f7472795f53
6f7272795f666f72
5f746861747d2066
6c61672069732066
6c61677b444e535f
5333637572313779
5f5431303731217d
20666c6167206973
20666c61677b3768
335f6b33795f3135
5f35336375723137
797d323232323232
```
これを`From Hex`に書けると、ダミーにまぎれて正解のFLAGが含まれている。

### browser_db
与えられたファイルをsqliteで確認。Firefoxのデータぽい。  
`.tables`でテーブル名を取得。
`sqlite> select * from moz_places;`で 得られた履歴の1つにフラグを含むものがある。
```
16|https://duckduckgo.com/?q=flag%7Bgoosegoosego%7D&t=ffab|flag{goosegoosego} at DuckDuckGo|moc.ogkcudkcud.|1|0|0|98|1626633856437000|2a3ozo4TBTpD|0|47356609649855|DuckDuckGo. Privacy, Simplified.||4
```


## OSINT
### tkys_with_love
コールサイン `C6DF6` でググるとすぐに見つかる。

### Dorks
URLに特定の文字が含まれるサイトを検索する場合は、`inurl:`を使う。

### filters_op
特定ユーザ、特定日付のツイートを検索。  
untilが5/15だと何も合致しない。
> from:cas_nisc since:2017-05-15 until:2017-05-16

このツイートが正解。  
https://twitter.com/cas_nisc/status/864058208536543232

### tkys_eys_only
画像に映っているURLを確認し、Googleマップを開く。
「40.749444N 73.9680568W」の地点に国際連合本部ビルがある。  
組織名を英語で回答すれば正解。  

### MITRE
与えられた文字Txxxxは、MITRE ATT&CKのテクニックの識別子。  
各テクニックの先頭文字を並べていけばよい。
T1170ならMshtaなので、「M」。

### MAC
与えられた英数字はMACアドレスにおけるベンダーコード。  
`2C:C2:60`はOracle Corporation。それぞれの先頭を並べれば正解。

### Ropeway
写真が与えられ、ロープウェイの名前を答える。  
画像検索してもよさそうだが、遊園地が見えたので、「ロープウェイ 海 遊園地」で検索。  
海じゃなくて湖だったようだが、「かんざんじロープウェイ」が見つかる。  


## pwn
### tkys_let_die
ソースが添付されていることに気づかないまま解いてしまった。。。  
ターミナルに入り、コマンドを打つと名前を聞かれる。  
適当に入力すると
```
Gate is close.
Goodbay hoge.
```

長い文字列を入れてみる。  
```
You'll need permission to pass. What's your name?
> 1111111111111111111111111111111111111111111111111111111111111111111111
Gate is 111111.
Goodbay 11111111111111111111111111111111.
```

Gate isのところに、名前の一部が返ってくる。
```
You'll need permission to pass. What's your name?
> 111111111111111111111111111222222222222222222222223333333333333333333
Gate is 122222.
Goodbay 11111111111111111111111111122222.
```

何文字目か分かったので、openが入るように調整してみる。  
```
You'll need permission to pass. What's your name?
> 11111111111111111111111111open   

 =============================

     GREAT! GATE IS OPEN!!

 >> Flag is
 ```


## Programing

### echo_me
```python
from netcat import Netcat                                                                                                                 

nc = Netcat('10.1.1.10', 12020)

while True:
    X = nc.read_until('\n')
    print(X,"x")
    q = nc.read_until('\n')
    print(q,"q")
    ans = q[9:17]
    print(ans)
    nc.write(str(ans + '\n').encode('utf-8'))
    Y = nc.read_until('\n')
    print(Y,"y")
    Z = nc.read_until('\n')
    print(Z,"z")
    A = nc.read_until('\n')
    print(Y,"a")
```

### deep_thought
Python Netcat · GitHub  
https://gist.github.com/leonjza/f35a7252babdf77c8421
```python
from netcat import Netcat                                                                                                                 

nc = Netcat('10.1.1.10', 12010)

while True:
    X = nc.read_until('\n')
    print(X)
    q = nc.read_until('\n')
    print(q)
    ans = str(eval(q.strip('\ufeff').strip('\n')))
    print(ans)
    nc.write(str(ans + '\n').encode('utf-8'))
    Y = nc.read_until('\n')
    print(Y)
    Z = nc.read_until('\n')
    print(Z)
```

以下、最終的な回答状況。  
![image](https://user-images.githubusercontent.com/42441861/134931550-c7de1039-ebe6-4764-a2f8-0cbf40093044.png)


# ハッキングコンテスト in 京都スマートシティエキスポ2021
- 2021/12/18 10:00-15:00, 2021/12/19 10:00-15:00で開催。  
- 参加者27チーム（人）中、750点で5位。

## Misc
### Q1. 「先斗町　秋の特別キャンペーン情報」と題した謎のメールが送られてきた。本文の一部が暗号化されているようだ。
`FLAG{5eb6fcfbcde3bd06b9f11f1b018b79c172623a2c}`

ASCIIコードかと思ったが、`5e`は良いが`b6`は該当なし。そもそもASCIIコードは暗号化ではない。  
40文字であることからSHA1ハッシュとあたりを付けて、 https://crackstation.net/ で検索。    
`5eb6fcfbcde3bd06b9f11f1b018b79c172623a2c	sha1	kyotocity`

`FLAG{kyotocity}`で正解。

## SD
### Q2-1. 京都府から塩小路通にあるお店に設置されているWebカメラに使われていたマイクロSDのイメージを調査するように依頼がきた。 どのようなファイルが入っているか調査してみよう。
とりあえずそのままstringコマンド実行すると答えが見えた。
```cmd
>strings sd.img | findstr FLAG
FLAG{NLFNASUKELFWJUBLZ}
```
### Q2-2.他にもFLAGがあるらしい。
Linuxに切り替えてmountしてみる。  
秘密鍵が置かれていたので、復号するとFLAGを得られた。  
```shell
$ sudo mount sd.img /mnt/sd
$ ls -la -R
.:
合計 24
drwxr-xr-x  4 root root 4096 12月 12 02:49 .
drwxr-xr-x 18 root root 4096 12月 18 13:20 ..
-rw-r--r--  1 root root  256 12月 12 02:34 .flag.encrypted
drwxr-xr-x  2 root root 4096 12月 12 02:34 .keys
-rw-r--r--  1 root root   24 12月 12 02:15 flag.txt
drwxr-xr-x  2 root root 4096 12月 12 02:49 hello

./.keys:
合計 16
drwxr-xr-x 2 root root 4096 12月 12 02:34 .
drwxr-xr-x 4 root root 4096 12月 12 02:49 ..
-rw-rw-rw- 1 root root 1679 12月 12 02:34 enc.key
-rw-rw-rw- 1 root root  451 12月 12 02:34 enc.pub

./hello:
合計 8
drwxr-xr-x 2 root root 4096 12月 12 02:49 .
drwxr-xr-x 4 root root 4096 12月 12 02:49 ..
$ 
$ sudo cat .flag.encrypted | openssl rsautl -decrypt -inkey ./.keys/enc.key
FLAG{EZKW_MFTTVGXVTPVARN}
```

## Webカメラ
### Q3-1. 前任者が引継ぎ前に退職してしまった。 webカメラへのアクセスはこのファイルのみが手掛かりだという...
Q3-1.pcapngを与えられる。WireSharkで開き、追跡→TCPストリーム。  

あるIPアドレスの/loginに対して、ID`manager@example.com`とパスワード`secret@password`で認証している様子。  
ブラウザで実際にアクセスすると、ログイン後ページにFLAGが書かれていた。
`FLAG{DVZ-AJHZUAUBHGB}`

### Q3-2.前任者からのメッセージが、残されているようだ。
Webcam01から04に静止画が表示されているが、04だけあからさまに怪しい黒塗り。  

testtesttest.pngをダウンロードし、うさみみハリケーンのステガノグラフィー解析に投入。  
「次ローテート 5回」などを適用すると読める状態になる。  
`FLAG{TYNDKSREVCBKL}`

### Q3-3.京都タワー内のカメラはもっとあるはずだが、確認することができない。かくされているのか？
4つしかカメラ画像が表示されていないが、まだあるらしい。  
検索機能に`'OR''=''--;`を入力し、SQLインジェクションを実施する。

Webcam05とWebcam06が現れ、06に`FLAG{9XG9KU8UHWQEDE3}`が表示されている。

## NAS
### Q4-3.
控えていなかった。

## スマートリモコン
解けなかった。最後までsolved:0だったような？

## モバイルアプリ
### Q6-1.京都市内の大学の友人がアプリを作ったらしい。インストールして実行してみたが、何も表示されない。 原因を調査してみよう。
MobSFを起動し、q1.apkを突っ込む。

CODE ANALYSISにInfoで「The App logs information. Sensitive information should never be logged.」が検出されていたので該当ファイル`MainActivity.java`を開くとFLAGがベタ書きされている。
```java
package com.example.sp_q1;
import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;
import kotlin.Metadata;
/* compiled from: MainActivity.kt */
@Metadata(d1 = {"\u0000\u0018\n\u0002\u0018\u0002\n\u0002\u0018\u0002\n\u0002\b\u0002\n\u0002\u0010\u0002\n\u0000\n\u0002\u0018\u0002\n\u0000\u0018\u00002\u00020\u0001B\u0005¢\u0006\u0002\u0010\u0002J\u0012\u0010\u0003\u001a\u00020\u00042\b\u0010\u0005\u001a\u0004\u0018\u00010\u0006H\u0014¨\u0006\u0007"}, d2 = {"Lcom/example/sp_q1/MainActivity;", "Landroidx/appcompat/app/AppCompatActivity;", "()V", "onCreate", "", "savedInstanceState", "Landroid/os/Bundle;", "app_release"}, k = 1, mv = {1, 5, 1}, xi = 48)
/* loaded from: classes.dex */
public final class MainActivity extends AppCompatActivity {
    /* JADX INFO: Access modifiers changed from: protected */
    @Override // androidx.fragment.app.FragmentActivity, androidx.activity.ComponentActivity, androidx.core.app.ComponentActivity, android.app.Activity
    public void onCreate(Bundle bundle) {
        System.out.println((Object) "FLAG{LV3U9UCUV393NX5}");
        super.onCreate(bundle);
        setContentView(R.layout.activity_main);
    }
}
```

### Q6-2.またもや京都市内の大学の友人がアプリを作ったらしい。 セキュリティに問題がないか調査を依頼された。
こちらもMobSFに突っ込み、CODE ANALYSISからあたりを付ける。  
```java
    public final String msgdecrypt(String str) {
        Intrinsics.checkNotNullParameter(str, "ret");
        Charset charset = StandardCharsets.UTF_8;
        Intrinsics.checkNotNullExpressionValue(charset, "UTF_8");
        byte[] bytes = str.getBytes(charset);
        Intrinsics.checkNotNullExpressionValue(bytes, "(this as java.lang.String).getBytes(charset)");
        Cipher instance = Cipher.getInstance("AES_128/ECB/PKCS5Padding");
        byte[] bytes2 = "hogefugahogefuga".getBytes(Charsets.UTF_8);
        Intrinsics.checkNotNullExpressionValue(bytes2, "(this as java.lang.String).getBytes(charset)");
        SecretKeySpec secretKeySpec = new SecretKeySpec(bytes2, "AES_128/ECB/PKCS5Padding");
        instance.init(1, secretKeySpec);
        Cipher instance2 = Cipher.getInstance("AES_128/ECB/PKCS5Padding");
        instance2.init(2, secretKeySpec);
        try {
            byte[] doFinal = instance2.doFinal(Base64.getDecoder().decode(bytes));
            Intrinsics.checkNotNullExpressionValue(doFinal, "decrypted");
            Charset defaultCharset = Charset.defaultCharset();
            Intrinsics.checkNotNullExpressionValue(defaultCharset, "defaultCharset()");
            return new String(doFinal, defaultCharset);
        } catch (Exception unused) {
            return "Error";
        }
    }
    // 中略
    /* renamed from: onViewCreated$lambda-0 */
    public static final void m23onViewCreated$lambda0(FirstFragment firstFragment, View view) {
        Intrinsics.checkNotNullParameter(firstFragment, "this$0");
        FragmentKt.findNavController(firstFragment).navigate(R.id.action_FirstFragment_to_SecondFragment);
        System.out.println((Object) firstFragment.msgdecrypt("BLtodRq3zO/7Ys70UxsMgg=="));
        System.out.println((Object) "Z8QpJUQgXeA2h8lMn0NAhjLmnPNYS35Ye5ycDXFXh6k=");
    }
```
msgdecryptの中で、key`hogefugahogefuga`で復号している。  

CyberChefを使い、`Z8QpJUQgXeA2h8lMn0NAhjLmnPNYS35Ye5ycDXFXh6k=`をBase64でコード後にAES Decryptしてみると`FLAG{wmxgzknhnhijwysrb}`が出てきた。  
https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true)AES_Decrypt(%7B'option':'UTF8','string':'hogefugahogefuga'%7D,%7B'option':'Hex','string':''%7D,'ECB','Raw','Raw',%7B'option':'Hex','string':''%7D,%7B'option':'Hex','string':''%7D)&input=WjhRcEpVUWdYZUEyaDhsTW4wTkFoakxtblBOWVMzNVllNXljRFhGWGg2az0

## 顔認証
手つかず

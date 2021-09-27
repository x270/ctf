## Web
### web_meta
HTMLファイルを開いて、metaタグに書かれているflagを回答。

## Rev
### protected
```bash
[x270@X270]$ ./chall
PASSWORD: sUp3r_s3Cr37_P4s5w0Rd
nitic_ctf{hardcode_secret}
[x270@X270]$ ./chall
PASSWORD: hogehoge
Invalid password.
[x270@X270]$ strings chall
/lib64/ld-linux-x86-64.so.2
libc.so.6
__isoc99_scanf
puts
__stack_chk_fail
printf
strlen
malloc
__cxa_finalize
strcmp
__libc_start_main
GLIBC_2.7
GLIBC_2.4
GLIBC_2.2.5
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
u+UH
ATSH
0[A\]
[]A\A]A^A_
PASSWORD:
sUp3r_s3Cr37_P4s5w0Rd
Invalid password.
```

## Caesar Cipher
fdhvdu  
CyberCheffで23文字ずらすと正解。  
https://gchq.github.io/CyberChef/#recipe=ROT13(true,true,false,23)&input=ZmRodmR1

## pwn monster 1
```shell
[x270@X270]$ nc 35.200.120.35 9001
 ____                 __  __                 _
|  _ \__      ___ __ |  \/  | ___  _ __  ___| |_ ___ _ __
| |_) \ \ /\ / / '_ \| |\/| |/ _ \| '_ \/ __| __/ _ \ '__|
|  __/ \ V  V /| | | | |  | | (_) | | | \__ \ ||  __/ |
|_|     \_/\_/ |_| |_|_|  |_|\___/|_| |_|___/\__\___|_|
                        Press Any Key


Welcome to Pwn Monster World!

I'll give your first monster!

Let's give your monster a name!
+--------+--------------------+----------------------+
|name    | 0x0000000000000000 |                      |
|        | 0x0000000000000000 |                      |
|HP      | 0x0000000000000064 |                  100 |
|ATK     | 0x000000000000000a |                   10 |
+--------+--------------------+----------------------+
Input name: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
+--------+--------------------+----------------------+
|name    | 0x4141414141414141 |             AAAAAAAA |
|        | 0x4141414141414141 |             AAAAAAAA |
|HP      | 0x4141414141414141 |  4702111234474983745 |
|ATK     | 0x4141414141414141 |  4702111234474983745 |
+--------+--------------------+----------------------+
OK, Nice name.

Let's battle with Rival! If you win, give you FLAG.

[You] AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAApwnchu HP: 4702111234474983745
[Rival] pwnchu HP: 9999
Your Turn.

Rival monster took 4702111234474983745 damage!


[You] AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAApwnchu HP: 4702111234474983745
[Rival] pwnchu HP: -4702111234474973746
Win!

nitic_ctf{We1c0me_t0_pwn_w0r1d!}

*** stack smashing detected ***: terminated
Aborted (core dumped)
```

### tanitu_kanji
タイトル通りの単一換字。  
換字用の辞書？がソースコード中に2種類書かれている。
```python
alphab = "abcdefghijklmnopqrstuvwxyz0123456789{}_"
after1 = "fl38ztrx6q027k9e5su}dwp{o_bynhm14aicjgv"
after2 = "rho5b3k17pi_eytm2f94ujxsdvgcwl{}a086znq"
```
formatの各桁が`1`か其れ以外かで、どちらの辞書を使うかを切り替えるらしい。  
formatは10桁であることから、辞書を計10回通ることが分かる。
`0000000000`から`1111111111`までの`01`を一通り試せばよいので、
afterからalphabetの方に戻すコードに書き換えてみる。

```python
import os
alphab = "abcdefghijklmnopqrstuvwxyz0123456789{}_"
after1 = "fl38ztrx6q027k9e5su}dwp{o_bynhm14aicjgv"
after2 = "rho5b3k17pi_eytm2f94ujxsdvgcwl{}a086znq"

ans = "l0d0pipdave0dia244im6fsp8x"
format = ""

for x in range(1024):
    format = bin(x)[2:].zfill(10)
    print(format)
    flag = ans

    def conv(s:str, table:str) -> str:
            res = ""
            for c in s:
                     i = table.index(c)
                     res += alphab[i]
            return res

    for f in format:
            if f == "1":
                    flag = conv(flag, after1)
            else:
                    flag = conv(flag, after2)
    print(flag)
```
実行してみたら、ちゃんとそれらしい文字列に戻ってくれた。
```sh
[x270@X270]$ python3 kanji2.py  > ans.txt
[x270@X270]$ cat ans.txt | grep ctf
nitic_ctf{bit_full_search}
```

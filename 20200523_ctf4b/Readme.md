# SECCON Beginners CTF 2020
- 2020/5/23～5/24開催  
https://www.seccon.jp/2020/beginners/about-seccon-beginners.html

## R&B [Crypt]
ROT13とBase64を繰り返すコードとその結果が与えられる。
```Python:problem.py
from os import getenv


FLAG = getenv("FLAG")
FORMAT = getenv("FORMAT")


def rot13(s):
    # snipped


def base64(s):
    # snipped


for t in FORMAT:
    if t == "R":
        FLAG = "R" + rot13(FLAG)
    if t == "B":
        FLAG = "B" + base64(FLAG)

print(FLAG)
```
```
BQlVrOUllRGxXY2xGNVJuQjRkVFZ5U0VVMGNVZEpiRVpTZVZadmQwOWhTVEIxTkhKTFNWSkdWRUZIUlRGWFUwRklUVlpJTVhGc1NFaDFaVVY1Ukd0Rk1qbDFSM3BuVjFwNGVXVkdWWEZYU0RCTldFZ3dRVmR5VVZOTGNGSjFTMjR6VjBWSE1rMVRXak5KV1hCTGVYZEplR3BzY0VsamJFaGhlV0pGUjFOUFNEQk5Wa1pIVFZaYVVqRm9TbUZqWVhKU2NVaElNM0ZTY25kSU1VWlJUMkZJVWsxV1NESjFhVnBVY0d0R1NIVXhUVEJ4TmsweFYyeEdNVUUxUlRCNVIwa3djVmRNYlVGclJUQXhURVZIVGpWR1ZVOVpja2x4UVZwVVFURkZVblZYYmxOaWFrRktTVlJJWVhsTFJFbFhRVUY0UlZkSk1YRlRiMGcwTlE9PQ==
```
ROT13のときは先頭にR、Base64のときは先頭にBを付けてくれているので、先頭文字を見て逆に戻していく。

```Python
import codecs
flg = open("encoded_flag", "r").read()
while flg[0] in "RB": 
	if flg[0] == "R":
		flg = codecs.decode(flg[1:], "rot13")
	else:
		flg = codecs.decode(flg[1:], "base64")
print(flg)
```
> ctf4b{rot_base_rot_base_rot_base_base}

## emoemoencode [Misc]
絵文字の羅列が与えられる。
```
🍣🍴🍦🌴🍢🍻🍳🍴🍥🍧🍡🍮🌰🍧🍲🍡🍰🍨🍹🍟🍢🍹🍟🍥🍭🌰🌰🌰🌰🌰🌰🍪🍩🍽
```
先頭の```🍣```を見て、関係ないけど「寿司ビール問題」なんてのがあったな、など思う。  
おそらくフラグの文字コードを大幅にずらしてUnicode絵文字で表現してるのだろうとあたりをつけ、文字コードを調べる。

|  絵文字  |  Unicode  | 想定 | ASCIIコード |
| :---: | :---: | :---: | :---: | 
|  🍣  |  U+1F363  | c | 0x63 |
|  🍴  |  U+1F374	  | t |0x74 |
|  🍦  |  U+1F366  | f |0x66 |

想定通り末尾が同じと分かるので、コードを調べて先頭を取っ払ってみる。  
CyberChefで下のように実行。

Input ```🍣🍴🍦🌴🍢🍻🍳🍴🍥🍧🍡🍮🌰🍧🍲🍡🍰🍨🍹🍟🍢🍹🍟🍥🍭🌰🌰🌰🌰🌰🌰🍪🍩🍽```  
Recipe ```To Charcode```  
Output ```01f363 01f374 01f366 01f334 01f362 01f37b 01f373 01f374 01f365 01f367 01f361 01f36e 01f330 01f367 01f372 01f361 01f370 01f368 01f379 01f35f 01f362 01f379 01f35f 01f365 01f36d 01f330 01f330 01f330 01f330 01f330 01f330 01f36a 01f369 01f37d```

Outputの「01f3」を全置換で削除し、Inputに入れなおす。

Input ```63 74 66 34 62 7b 73 74 65 67 61 6e 30 67 72 61 70 68 79 5f 62 79 5f 65 6d 30 30 30 30 30 30 6a 69 7d```  
Recipe ```From Charcode```  
Output ```ctf4b{stegan0graphy_by_em000000ji}```

> ctf4b{stegan0graphy_by_em000000ji}

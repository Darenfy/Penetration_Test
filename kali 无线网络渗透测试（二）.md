## 无线网络监听模式
#### 网络监听原理
无线网卡有四种工作模式
- Managed 模式
主要用于无线客户端与 AP 进行通信
- Master 模式
部分网卡支持该模式，即可以使得设备本身成为无线接入点 AP
- Ad-Hoc 模式
可以理解为在一个网络中，所有设备对等，没有管理者
- Monitor 模式
用于监控无线网络内部的流量

一般情况下，网卡都处在管理模式，就是只负责把数据包传送给所有主机，不搞事。
当网卡处于监听模式时，将获取在局域网中的所有数据包，不主动与其他主机交换信息，不修改在网络中传输的数据包


#### 设置监听模式
**2.4G 网卡**
查看无线网卡工作模式
```bash
root@kali:~# iwconfig wlan0
wlan0     unassociated  ESSID:""  Nickname:"<WIFI@REALTEK>"
          Mode:Managed  Frequency=2.462 GHz  Access Point: Not-Associated
          Sensitivity:0/0  
          Retry:off   RTS thr:off   Fragment thr:off
          Encryption key:off
          Power Management:off
          Link Quality=0/100  Signal level=0 dBm  Noise level=0 dBm
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:0  Invalid misc:0   Missed beacon:0
```
开启监听模式
```bash
root@kali:~# airmon-ng start wlan0

PHY     Interface       Driver          Chipset

phy0    wlan0           88XXau          Realtek Semiconductor Corp. RTL8812AU 802.11a/b/g/n/ac 2T2R DB WLAN Adapter
                (mac80211 monitor mode already enabled for [phy0]wlan0 on [phy0]wlan0)


root@kali:~# iwconfig wlan0
wlan0     unassociated  ESSID:""  Nickname:"<WIFI@REALTEK>"
          Mode:Monitor  Frequency=2.457 GHz  Access Point: Not-Associated   
          Sensitivity:0/0  
          Retry:off   RTS thr:off   Fragment thr:off
          Encryption key:off
          Power Management:off
          Link Quality=0/100  Signal level=0 dBm  Noise level=0 dBm
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:0  Invalid misc:0   Missed beacon:0
```

关闭监听模式命令
```bash
root@kali:~# airmon-ng stop wlan0
```
**5G 网卡**
查看无线网卡接口工作模式
```bash
root@kali:~# iwconfig 
lo        no wireless extensions.

eth0      no wireless extensions.

wlan0     IEEE 802.11AC  ESSID:"niubi_5G"  Nickname:"<WIFI@REALTEK>"
          Mode:Managed  Frequency:5.745 GHz  Access Point: C4:06:83:7C:CD:78   
          Bit Rate:867 Mb/s   Sensitivity:0/0  
          Retry:off   RTS thr:off   Fragment thr:off
          Encryption key:****-****-****-****-****-****-****-****   Security mode:open
          Power Management:off
          Link Quality=57/100  Signal level=0 dBm  Noise level=0 dBm
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:0  Invalid misc:0   Missed beacon:0
```
开启监听模式
```bash
root@kali:~# ip link set wlan0 down 
root@kali:~# iwconfig wlan0 mode monitor 
root@kali:~# ip link set wlan0 up
root@kali:~# iwconfig wlan0 
wlan0     IEEE 802.11AC  ESSID:"niubi_5G"  Nickname:"<WIFI@REALTEK>"
          Mode:Monitor  Frequency:5.745 GHz  Access Point: C4:06:83:7C:CD:78   
          Sensitivity:0/0  
          Retry:off   RTS thr:off   Fragment thr:off
          Encryption key:off
          Power Management:off
          Link Quality=1/100  Signal level=-99 dBm  Noise level=0 dBm
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:0  Invalid misc:0   Missed beacon:0
```
关闭监听模式
```bash
root@kali:~# ip link set wlan0 down 
root@kali:~# iwconfig wlan0 mode managed
root@kali:~# ip link set wlan0 up
root@kali:~# iwconfig wlan0 
wlan0     IEEE 802.11bgn  ESSID:"niubi"  Nickname:"<WIFI@REALTEK>"
          Mode:Managed  Frequency:5.745 GHz  Access Point: C4:06:83:7C:CD:78   
          Bit Rate:144.4 Mb/s   Sensitivity:0/0  
          Retry:off   RTS thr:off   Fragment thr:off
          Encryption key:****-****-****-****-****-****-****-****   Security mode:open
          Power Management:off
          Link Quality=100/100  Signal level=-18 dBm  Noise level=0 dBm
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:0  Invalid misc:0   Missed beacon:0

```

## 扫描无线网络
#### 扫描方式
主动扫描：主动向 ``AP`` 发送探索请求帧，可以扫描到不发送信号的 ``AP``

被动扫描：利用 ``AP`` 每隔一段时间自动发送的信号帧进行扫描，无法发现不自动广播信号帧的 ``AP``

#### 扫描``AP``
**实时扫描**
```bash
 CH  7 ][ Elapsed: 42 s ][ 2021-03-08 23:36 ][ interface wlan0 down                                                                                                                                                                                                                 
#AP MAC                  通告编号                   最大速率     加密算法     路由器名称
 BSSID              PWR  Beacons    #Data, #/s  CH   MB   ENC CIPHER  AUTH ESSID          
#                 信号强度          数据吞吐量    频道      加密体系      认证协议
                                                                                                                                          
 00:00:00:00:00:00   -1        0        0    0  -1   -1                    <length:  0>                                                   
 C4:06:83:7C:CD:7A   -8        5        0    0 149  780   WPA2 CCMP   PSK  <length:  0>                                                   
 C4:06:83:7C:CD:78   -8        6        1    0 149  780   WPA2 CCMP   PSK  niubi_5G                                                       
 C4:06:83:FC:CD:78  -10        5        0    0 149  780   WPA2 CCMP   PSK  <length:  0>                                                   
 88:C3:97:E8:E9:43  -12        3        0    0 161 1733   WPA2 CCMP   PSK  Xiaomi_E940_5G                                                 
 88:C3:97:E8:E9:42  -19       56        2    0   1  270   WPA2 CCMP   PSK  Xiaomi_E940                                                    
 C4:06:83:7C:CD:74  -24       32        2    0   1  270   WPA2 CCMP   PSK  niubi                                                          
 C4:06:83:7C:CD:75  -25       53        0    0   1  270   WPA2 CCMP   PSK  <length:  0>                                                   
 C4:06:83:7C:CD:79  -25       58        0    0   1  270   WPA2 CCMP   PSK  <length:  0>                                                                       
 70:8B:CD:5D:36:50  -46       43        7    0  10  130   WPA2 CCMP   PSK  ASUS                                                           
 A2:9D:7E:42:31:3E  -45       71        0    0   6  360   OPN              <length:  0>                                                 
 94:D9:B3:4D:A8:5C  -66       42        0    0   1  270   WPA2 CCMP   PSK  TP-LINK_2333                                                   
 48:0E:EC:16:98:E3  -67       41        0    0   1  270   WPA2 CCMP   PSK  TP-LINK_98E3 
 F8:9A:78:95:DF:C5  -71       32        0    0   1  270   WPA2 CCMP   PSK  <length:  0>                                                        
 42:B0:76:50:DF:C0  -71       16        0    0  12  130   WPA2 CCMP   PSK  ASUS_CC       

#                 客户端 MAC                传输率        客户端发送分组数量  被客户端查探的 ESSID
 BSSID              STATION            PWR   Rate    Lost    Frames  Notes  Probes   
                                                                                                                         
 (not associated)   C8:E2:65:A1:2B:3D  -73    0 - 1      0        2                                                                       
 (not associated)   5C:CF:7F:BA:2A:70  -85    0 - 1      0        1         HUST_WIRELESS_AUTO                                            
 (not associated)   D0:BA:E4:64:1A:B9  -71    0 - 1      0        2                                                                       
 88:C3:97:E8:E9:42  A4:83:E7:4C:CF:11  -27    0 -11      0        1                                                                       
Quitting...
```
**离线扫描**
```bash
$ airodump-ng -r <.pcap>
```
**获取 AP 的生产厂商**
```bash
root@kali:~/Desktop# airodump-ng wlan0 --manufacturer
 CH 13 ][ Elapsed: 12 s ][ 2021-03-09 00:13                                                                                                                                                                                                                            
 BSSID              PWR  Beacons    #Data, #/s  CH   MB   ENC CIPHER  AUTH ESSI                 MANUFACTURER                             
                                                                                                                                                     
 8C:DE:F9:33:59:C3  -85        2        0    0  40 1733   WPA2 CCMP   PSK  Redmi_840D_5G         Unknown                                                               
 C4:36:55:21:E1:79  -80        2        0    0  11  270   WPA2 CCMP   PSK  360WiFi-21E179        Shenzhen Fenglian Technology Co., Ltd.  
 42:B0:76:50:DF:C0  -75        7        0    0  12  130   WPA2 CCMP   PSK  ASUS_CC               Unknown                                 
 CC:08:FB:07:77:72  -48       10        0    0  11  270   WPA2 CCMP   PSK  TP-LINK_7772          TP-LINK TECHNOLOGIES CO.,LTD.                                                 
 70:8B:CD:5D:36:50  -51        5        1    0  10  130   WPA2 CCMP   PSK  ASUS                  ASUSTek COMPUTER INC.                   
 2E:D1:27:80:4D:18  -62       10        0    0   7  360   OPN              <length:  0>          Unknown                                                            
 34:CE:00:46:53:E2  -75       12        0    0   7  130   WPA2 CCMP   PSK  DD                    XIAOMI Electronics,CO.,LTD              
 A2:9D:7E:42:31:3E  -44       11        0    0   6  360   OPN              <length:  0>          Unknown                                                             
 94:D9:B3:4D:A8:5C  -60        6        2    0   1  270   WPA2 CCMP   PSK  TP-LINK_2333          TP-LINK TECHNOLOGIES CO.,LTD.           
 60:EE:5C:CD:9C:62  -64        5        0    0   1  270   WPA2 CCMP   PSK  shcgx                 SHENZHEN FAST TECHNOLOGIES CO.,LTD      
```
**扫描开启 WPS 功能的 AP**
```bash
root@kali:~/Desktop#  wash -i wlan0
#MAC               信道     WPS版本     生产厂商   
BSSID               Ch  dBm  WPS  Lck  Vendor    ESSID
#                       强度     WPS锁定         AP SSID
--------------------------------------------------------------------------------
F8:9A:78:95:DF:C4    1  -70  2.0  No   Unknown   yqZhu
F8:9A:78:95:DF:C9    1  -72  2.0  No   Unknown   (null)
88:C3:97:E8:E9:42    1  -24  2.0  No   RalinkTe  Xiaomi_E940
C4:06:83:7C:CD:79    1  -16  2.0  No   Unknown   (null)
C4:06:83:7C:CD:74    1  -16  2.0  No   Unknown   niubi
```
**获取隐藏的 ESSID**
```bash
root@kali:~/Desktop# mdk3 wlan0 p -b a -t C4:06:83:7C:CD:74 -s 100

SSID Bruteforce Mode activated!

Waiting for beacon frame from target...
Sniffer thread started
...

SSID does not seem to be hidden! Found: "niubi"
```
**获取 AP 漏洞信息**
根据 MAC 查询生产厂商：https://mac.bmcx.com/
路由器公开漏洞库：http://routerpwn.com/

#### 扫描客户端
**扫描记录客户端**
使用 ``Airodump-ng`` 扫描时，即可扫描到连接到其 ``AP`` 的客户端

**扫描未关联客户端**
关联：客户端发送关联请求包给目标 AP，以获取 AP 关联响应

```bash
root@kali:~/Desktop# git clone http://github.com/xme/hoover
Cloning into 'hoover'...
warning: redirecting to https://github.com/xme/hoover/
remote: Enumerating objects: 12, done.
remote: Total 12 (delta 0), reused 0 (delta 0), pack-reused 12
Receiving objects: 100% (12/12), 7.27 KiB | 7.27 MiB/s, done.
Resolving deltas: 100% (2/2), done.
root@kali:~/Desktop# cd hoover
root@kali:~/Desktop/hoover# ./hoover.pl --interface=wlan0 --tshark-path=/usr/bin/tshark --dumpfile=result.txt
Running as user "root" and group "root". This could be dangerous.
Capturing on 'wlan0'
1 ++ New probe request from d0:ba:e4:64:1a:b9 with SSID: Wildcard (Broadcast) [1]
3 ++ New probe request from 7e:b5:9b:ae:22:f3 with SSID: Lh7ph9a17sqDY9aGWQBJvwlaGvb5SrrT [2]
9 ^C!! Received kill signal!
!! Dumping detected networks:
!! MAC Address          SSID                           Count      Last Seen
!! -------------------- ------------------------------ ---------- -------------------
!! 7e:b5:9b:ae:22:f3    Lh7ph9a17sqDY9aGWQBJvwlaGvb5SrrT          2 1969/12/31 19:00:07 
!! d0:ba:e4:64:1a:b9    Wildcard (Broadcast)                    7 1969/12/31 19:00:00 
!! Total unique SSID: 2
```
**查看 AP 与客户端关联关系**
```bash
root@kali:~/Desktop# airgraph-ng -i dump-01.csv -o Image.png -g CAPR 
Getting OUI file from http://standards-oui.ieee.org/oui.txt to /usr/share/airgraph-ng/
Completed Successfully

**** WARNING Images can be large, up to 12 Feet by 12 Feet****
Creating your Graph using, dump-01.csv and writing to, Image.png
Depending on your system this can take a bit. Please standby......
```



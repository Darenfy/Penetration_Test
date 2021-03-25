## 捕获数据包
将无线网卡设置为监听模式时，虽然可以嗅探网卡附近所有的数据包，但是无法直接获取到客户端的行为。
通过捕获数据包并分析，就可以得到客户端的行为.

#### 数据包概述
因为在监听模式下，可以捕获所有数据包，需要快速判断包的重要性，即要分析那些数据包，接下来是对数据包的一些分类。

**握手包**
采用 WPA 加密方式的无线 AP 和无线客户端进行连接前的认证信息包，其中包含后期数据传输的密钥。
注：在抓包的时候需要使客户端掉线，再次连接时，才能抓到握手包
握手包包括4 个交互过程，即 4 个握手包，协议为 EAPOL，编号 1 到 4
**非加密包**
没有经过加密而原始传输的数据包，数据包是否加密，取决于无线 AP
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210309180645851.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDIzMzgx,size_16,color_FFFFFF,t_70)
**加密包**
数据包使用不同的加密方式进行加密，需要解密后才可以看到原始数据包

####  802.11 帧概述
802.11 协议是无线局域网通用的标准，帧根据功能分为三类：管理帧，控制帧，数据帧

**数据帧**
无线 AP 和客户端之间传递数据
根据发送方和接收方不同，主要分为 4 小类：
IBSS - IEEE 802.11 标准模式，专为点对点连接的模式
TO AP
FROM AP
WDS - 无线分布式系统，可以让 AP 与 AP 之间进行通信

**控制帧**
竞争期间的握手通信和正向确认、结束非竞争期等
常用控制帧：
- RTS/CTS：虚拟载波监听
- ACK：确保数据到达
- PS-poll：减少工作站耗电

**管理帧**
无线客户端和 AP 之间的协商、关系的控制，如关联、认证、同步等

#### 捕获数据包
**设置监听信道**
无线网卡在监听时会不停的跳频，为防止漏掉重要的数据包，可手动设置监听信道
```bash
root@kali:~#  airmon-ng start wlan0 1

Found 2 processes that could cause trouble.
Kill them using 'airmon-ng check kill' before putting
the card in monitor mode, they will interfere by changing channels
and sometimes putting the interface back in managed mode

    PID Name
    514 NetworkManager
    652 wpa_supplicant

PHY     Interface       Driver          Chipset

phy0    wlan0           88XXau          Realtek Semiconductor Corp. RTL8812AU 802.11a/b/g/n/ac 2T2R DB WLAN Adapter
                (monitor mode enabled)

or
root@kali:~#  iwconfig wlan0 channel 1
```
**捕获数据包**
启动 ``wireshark``
![](https://img-blog.csdnimg.cn/20210309180708730.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDIzMzgx,size_16,color_FFFFFF,t_70)
**使用捕获过滤器**
``wireshark`` 在捕获数据包时会产生大量的冗余信息，使用捕获过滤器，仅捕获自己需要的数据包
```bash
格式
Protocal    指定协议

Direction   指定来源或目的地

Host(s)     主机地址，默认使用 host 关键字

Value       

Logical Operations 逻辑运算 可用 not | and | or

Other expression
```

#### 分析数据包
**显示过滤器**
在分析捕获的数据包之前，可以使用显示过滤器对其进行更进一步的过滤
```bash
格式
Protocol 协议
String1， String2 可选项
Comparison operator：指定比较运算符 eq ne gt lt ge le
Value
Logical Operations：指定逻辑运算符 and or not
Other expression：var=value
```

**常用过滤**
```bash
Beacons -> wlan.fc.type_subtype==0x08
可查看 AP SSID，MAC，当前信道，加密方式

Association -> wlan.fc.type_subtype==0x00
可查看客户端连接的 AP
```
![](https://img-blog.csdnimg.cn/20210309180726468.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDIzMzgx,size_16,color_FFFFFF,t_70)
#### 解密数据包
大部分 AP 都已经使用了加密手段，如果要对数据包进行分析，需要先进行解密。
**解密 WEP**
如果用户知道目标 AP 的密码，则可以解密数据包
添加密钥：
Edit -> Preference -> Protocols -> IEEE 802.11
-> Descryption keys Edit
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210309180746185.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDIzMzgx,size_16,color_FFFFFF,t_70)

**解密 WPA/WPA2**
同上位置添加解密密钥，注意格式
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210309180800962.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDIzMzgx,size_16,color_FFFFFF,t_70)
手动生成 ``PSK`` 值
https://www.wireshark.org/tools/wpa-psk.html
PassPhrase:密码
SSID：Wi-Fi 名称
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030918082041.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDIzMzgx,size_16,color_FFFFFF,t_70)
**永久解密**
```bash
# WEP
$ airdecap-ng -w [password] [pcap file]
password: 形式为 ASCII 的十六进制

# WPA
$ airdecap-ng -e [ESSID] -p [Password] [pcap file]
```

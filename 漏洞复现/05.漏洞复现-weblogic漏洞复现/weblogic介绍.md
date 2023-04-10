weblogic漏洞历史：

```shell
#控制台路径泄露
Weakpassword(重点)

#SSRF:
CVE-2014-4210(重点)

#JAVA反序列化:
CVE-2015-4852
CVE-2016-0638
CVE-2016-3510
CVE-2017-3248
CVE-2018-2628(重点)
CVE-2018-2893

#任意文件上传
CVE-2018-2894(重点)

#XMLDecoder反序列化:
CVE-2017-10271(重点)
CVE-2017-3506
```



```shell
#weakpassword:
Weblogic存在管理后台，通过账号密码登录，由于管理员的疏忽，经常会使用弱口令，或者默认的账户名密码。因此存在弱口令爆破的风险。在本环境下模拟了一个真实的weblogic环境，其后台存在一个弱口令，并且前台存在任意文件读取漏洞。分别通过这两种漏洞，模拟对weblogic场景的渗透。
```



```shell
# SSRF: CVE-2014-4210
Weblogic中存在一个SSRF漏洞，利用该漏洞可以发送任意HTTP请求，进而可以攻击内网中redis、fastcgi等脆弱组件。
```



```shell
# 任意文件上传漏洞: CVE-2018-2894
Oracle 7月更新中，修复了Weblogic Web Scrvice Test Page中一处任意文件上传漏洞，WebService Test Page 在“生产模式”下默认不开启，所以该漏洞有一定限制。利用该漏洞，可以上传任意jsp文件，进而获取服务器权限。
```



```shell
# XMLDecoder反序列化漏洞: CVE-2017-10271
Weblogic的WLS Security组件对外提供webservice服务，其中使用了XMLDecoder来解析用户传入的XML数据，在解析的过程中出现反序列化漏洞，导致可执行任意命令。
```



```shell
# Java反序列化漏洞: CVE-2018-2628
Oracle 2018年4月补丁中，修复了Weblogic Server WLS Core Components中出现的一个反序列化漏洞 (CVE-2018-2628) ，该漏洞通过t3协议触发，可导致未授权的用户在远程服务器执行任意命令。
```


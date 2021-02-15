# Linux 网络相关



```bash
# 查看网卡和IP
ifconfig

# 查看端口状态
netstat -anp

# 测试域名
ping www.google.com

# 监听指定端口并显示接收到的信息
nc -l 8888

# 测试指定端口能否连接(可以和 nc -l 连接)
telnet localhost 8888
```



## 软件防火墙 ufw

https://www.linode.com/docs/guides/configure-firewall-with-ufw/


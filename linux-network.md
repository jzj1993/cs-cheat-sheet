# Linux 网络相关



```bash
# 查看网卡和IP
ifconfig

# 查看端口状态
netstat -anp

# 测试域名
ping www.google.com

# 查看访问指定网址经过的设备和IP地址
traceroute www.google.com

# 监听指定端口并显示接收到的信息
nc -l 8888

# 测试指定端口能否连接(可以和 nc -l 连接)
telnet localhost 8888
```



## 修改网卡参数

```bash
# OpenWRT
# 修改网卡参数
vim /etc/config/network
# 重启网卡应用配置
/etc/init.d/network restart
```

示例配置文件

```bash
config interface 'loopback'
	option ifname 'lo'
	option proto 'static'
	option ipaddr '127.0.0.1'
	option netmask '255.0.0.0'

config globals 'globals'
	option ula_prefix 'fdbd:9c99:7971::/48'
	option multipath 'disable'
	option mptcp_path_manager 'fullmesh'
	option mptcp_scheduler 'default'
	option mptcp_checksum '0'
	option mptcp_debug '0'
	option mptcp_syn_retries '5'
	option mptcp_fullmesh_num_subflows '1'
	option mptcp_fullmesh_create_on_err '1'
	option mptcp_ndiffports_num_subflows '1'
	option congestion 'olia'

config interface 'lan'
	option type 'bridge'
	option ifname 'eth0'
	option proto 'static'
	option ipaddr '192.168.199.200'
	option netmask '255.255.255.0'
	option ip6assign '60'
	option multipath 'off'
	option gateway '192.168.199.1'
	option dns '192.168.199.1'

config interface 'wan'
	option ifname 'eth1'
	option proto 'dhcp'
	option auto '0'

config interface 'wan6'
	option ifname 'eth1'
	option proto 'dhcpv6'
	option auto '0'
```





## 命令行设置HTTP(S)代理

仅对部分会主动读取代理环境变量的软件有效，例如curl/wget。不同软件使用的格式可能不一样，全都设置上了。

```bash
_HTTP_PROXY=http://127.0.0.1:1087

setproxy() {
    export http_proxy=$_HTTP_PROXY;
    export https_proxy=$_HTTP_PROXY;
    export all_proxy=$_HTTP_PROXY;
    export HTTP_PROXY=$_HTTP_PROXY;
    export HTTPS_PROXY=$_HTTP_PROXY;
    export ALL_PROXY=$_HTTP_PROXY;
    echo "set http(s) proxy = $_HTTP_PROXY";
}

unsetproxy() {
    unset http_proxy;
    unset https_proxy;
    unset all_proxy;
    unset HTTP_PROXY;
    unset HTTPS_PROXY;
    unset ALL_PROXY;
    echo "unset http(s) proxy";
}
```



## git设置代理

git url 是 HTTP 形式：使用 `git config` 配置

```bash
git config --global http.proxy "http://127.0.0.1:8080"
git config --global https.proxy "http://127.0.0.1:8080"
# or
git config --global http.proxy "socks5://127.0.0.1:1080"
git config --global https.proxy "socks5://127.0.0.1:1080"
```

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

git url 是 SSH 形式：修改 `~/.ssh/config` 文件（不存在则新建）

```bash
Host github.com
   HostName github.com
   User git
   # 以下两者选一个
   # 1. 走 HTTP 代理
   # ProxyCommand socat - PROXY:127.0.0.1:%h:%p,proxyport=8080
   # 2. 走 socks5 代理（如 Shadowsocks）
   # ProxyCommand nc -v -x 127.0.0.1:1080 %h %p
```

参考 https://gist.github.com/chuyik/02d0d37a49edc162546441092efae6a1



## 软件防火墙 ufw

https://www.linode.com/docs/guides/configure-firewall-with-ufw/


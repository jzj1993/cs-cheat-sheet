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


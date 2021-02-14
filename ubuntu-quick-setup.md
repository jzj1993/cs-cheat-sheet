# Ubuntu常用环境配置



## apt更新

```bash
sudo apt update && sudo apt -y upgrade
```



## curl + wget + git + vim + ohmyzsh

```bash
sudo apt update && sudo apt install -y curl wget git vim
# OhMyZsh
sudo apt update && sudo apt install -y zsh
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

https://ohmyz.sh/



## ssh服务+密钥

连接远程主机、git仓库等，都需要密钥对。本机密钥生成步骤如下。

```bash
ssh-keygen -t rsa
```

https://git-scm.com/book/en/v2/Git-on-the-Server-Generating-Your-SSH-Public-Key 
https://www.cnblogs.com/kerrycode/p/9410928.html



有些主机需要自己配ssh-server，有些主机只需ssh密码登录后配置authorized_keys。

```bash
# 安装ssh server
sudo apt update && sudo apt install -y openssh-server
# 编辑配置文件
sudo vim /etc/ssh/sshd_config
```

修改为

```bash
RSAAuthentication yes
PubkeyAuthentication yes
PermitRootLogin yes
PasswordAuthentication yes
```

服务器上安装公钥 

```bash
sudo -i  # root用户
cd /root/.ssh  # 如果没有目录则先新建
echo "public key content" >> authorized_keys  # 添加本机公钥到远程，没有文件则创建
chmod 600 authorized_keys
chmod 700 /root/.ssh
```

密钥方式登录成功后，可编辑配置禁用密码登录 

```bash
PasswordAuthentication no
```

重启 SSH 服务

```bash
sudo service ssh restart
```

https://blog.csdn.net/permike/article/details/52386868



## FTP服务

```bash
# ftp server
sudo apt update && sudo apt install -y vsftpd
# 取消注释：local_enable=YES，允许本地用户登录，write_enable=YES，允许上传文件
sudo vim /etc/vsftpd.conf
# 注释掉root，即可root登录
sudo vim /etc/ftpusers
#重启服务
sudo /etc/init.d/vsftpd restart
```



## Docker

```bash
# Docker
sudo apt-get remove docker docker-engine docker.io containerd runc
sudo apt-get update && sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
# Optional: check fingerprint
# sudo apt-key fingerprint 0EBFCD88
# pub   rsa4096 2017-02-22 [SCEA]
#       9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
# uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
# sub   rsa4096 2017-02-22 [S]
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update && sudo apt-get install -y docker-ce docker-ce-cli containerd.io

# Docker Compose
# check latest version: https://github.com/docker/compose/releases
sudo curl -L "https://github.com/docker/compose/releases/download/1.28.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

https://docs.docker.com/engine/install/ubuntu/

https://docs.docker.com/compose/install/



## Node 10.x + Yarn + MongoDB + PM2套装

```bash
# Node 10.x
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt install -y nodejs

# Yarn
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update && sudo apt install -y yarn

# MongoDB
wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add -
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/4.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.2.list
sudo apt update && sudo apt install -y mongodb-org
sudo service mongod start
sudo systemctl enable mongodb

# PM2
sudo yarn global add pm2
sudo pm2 startup
```

https://nodejs.org/en/download/package-manager/ 
https://github.com/nodesource/distributions/blob/master/README.md 
https://yarnpkg.com/en/docs/install#debian-stable 
https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/



## 233v2ray: V2Ray + ShadowSocks

安装好后别忘了设置VPS防火墙

```bash
sudo -i # or su root
bash <(curl -s -L https://git.io/v2ray.sh)
```

https://github.com/233boy/v2ray/wiki/V2Ray%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E8%84%9A%E6%9C%AC



## ShadowSocks手动安装

```bash
sudo apt update && sudo apt install -y python-pip python-m2crypto && sudo pip install shadowsocks
# 编辑配置，server填0.0.0.0或服务器IP，可设置多个端口和密码；
# 防火墙打开ss要用的端口。推荐使用ufw命令管理端口。
sudo chmod 755 /etc/shadowsocks.json && sudo vim /etc/shadowsocks.json
```

```json
{
    "server":"0.0.0.0",
    "local_address":"127.0.0.1",
    "local_port":1080,
    "port_password": {
        "9005": "password1",
        "9006": "password2"
    },
    "timeout":600,
    "method":"aes-256-cfb",
    "fast_open":false
}
```

```bash
# 普通模式启动
sudo ssserver -c /etc/shadowsocks.json
# 查看端口状态
netstat -anp
# 客户端测试

# Daemon模式启动
sudo ssserver -c /etc/shadowsocks.json -d start
# 开机自启
sudo vi /etc/rc.local
```

在`exit 0`之前加一行 

```bash
/usr/local/bin/ssserver –c /etc/shadowsocks.json
```

还可以进一步用HaProxy中继实现加速：SSR客户端 <=> 国内VPS <=> 美国VPS

https://www.chedanji.com/ubuntu-shadowsocks/
https://linghucong.js.org/2016/04/20/setup-Shadowsocks-on-ubuntu-1604/
https://zhgcao.github.io/2016/05/10/ubuntu-install-shadowsocks/
https://github.com/shadowsocks/shadowsocks/issues/1027



## shell通过ss代理

```bash
vim ~/.bashrc
# or
vim ~/.zshrc
```

添加如下内容。使用时直接调用setproxy和unsetproxy即可开关shell代理（对wget等命令行工具有效）。

```bash
setproxy() {
    PROXY=http://127.0.0.1:1087;
    export http_proxy=$PROXY;
    export https_proxy=$PROXY;
    export all_proxy=$PROXY;
    export HTTP_PROXY=$PROXY;
    export HTTPS_PROXY=$PROXY;
    export ALL_PROXY=$PROXY;
    echo "set proxy=$PROXY";
}
unsetproxy() {
    unset http_proxy;
    unset https_proxy;
    unset all_proxy;
    unset HTTP_PROXY;
    unset HTTPS_PROXY;
    unset ALL_PROXY;
    echo "unset proxy";
}
```



## 图形界面开关

方式一：设置默认target 

```bash
sudo systemctl set-default multi-user.target
sudo systemctl set-default graphical.target
```

方式二：卸载lightdm 

```bash
sudo systemctl disable lightdm.service
sudo apt-get install --reinstall lightdm
```



## VNC

```bash
# install xfce desktop environment and vnc
sudo apt update && sudo apt install -y xfce4 vnc4server
# set vnc password
vncserver
# modify xstartup file (do not forget `chmod +x` to set executable if create a new one)
vim ~/.vnc/xstartup
```

```bash
#!/bin/sh
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
startxfce4 &

[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
xsetroot -solid grey
```

```bash
# restart vnc
vncserver -kill :1
vncserver
```



https://blog.csdn.net/m0_37041325/article/details/80516041


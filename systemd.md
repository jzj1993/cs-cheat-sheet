# systemd



## 简介

systemd是Ubuntu等系统自带的init system，用于管理系统服务启动.



## 安装

一般系统自带，不需要安装。安装可以用 `apt install systemd`。



## 模块

分为两个模块

- systemd
- journald



## 管理服务

```bash
# start/stop/restart/status
systemctl start/stop/restart/status NAME

# enable/disable service running when startup
systemctl enable/disable NAME
```

```bash
# list installed files
systemctl list-unit-files

# list units in memory
systemctl list-units

# list running units
systemctl list-units --state=running
```



## 查看日志

```bash
journalctl -u NAME -f

# 日志文件位置
cat /var/log/journal/NAME.service.log
```



## 创建/修改/删除Service

文件位置

```bash
/etc/systemd/system/NAME.service
# or
/usr/lib/systemd/system/NAME.service
# or old style
/etc/init.d/NAME
```

示例：
```
cat /usr/lib/systemd/system/bee.service
[Unit]
Description=Bee - Ethereum Swarm node
Documentation=https://docs.ethswarm.org
After=network.target

[Service]
EnvironmentFile=-/etc/default/bee
NoNewPrivileges=true
User=bee
Group=bee
ExecStart=/usr/bin/bee start --config /etc/bee/bee.yaml
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

停用Service --> 修改 --> 重新加载

```bash
# stop and disable service
systemctl stop NAME
systemctl disable NAME

# modify service file

# reload
systemctl daemon-reload
systemctl reset-failed
```



## Docker中替代方案

Docker中不能使用systemd
```
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down
```

By Design且不可修复
https://stackoverflow.com/questions/39169403/systemd-and-systemctl-within-ubuntu-docker-images
https://askubuntu.com/questions/813588/systemctl-failed-to-connect-to-bus-docker-ubuntu16-04-container

网上的做法没有用： `docker run -it --name xxx --privileged=true ubuntu bash`

替代方案
https://unix.stackexchange.com/questions/187042/starting-services-without-systemd
https://github.com/gdraheim/docker-systemctl-replacement

脚本
```bash
apt install -y python3

wget https://github.com/gdraheim/docker-systemctl-replacement/raw/master/files/docker/systemctl3.py -O /usr/bin/systemctl
wget https://github.com/gdraheim/docker-systemctl-replacement/raw/master/files/docker/journalctl3.py -O /usr/bin/journalctl
chmod +x /usr/bin/systemctl
chmod +x /usr/bin/journalctl
```



## Ubuntu中systemctl和service命令区别

service是一个wrapper script，兼容systemd等不同的init system.

https://askubuntu.com/questions/903354/difference-between-systemctl-and-service-commands



## service用法

```bash
service NAME start/stop/restart/status

# list all service, "+" started, "-" stopped, "?" unknown
service --status-all
[ + ]  acpid
[ - ]  apache2
[ + ]  apparmor
[ ? ]  apport
[ + ]  atd
[ ? ]  console-setup
[ + ]  cron
[ - ]  dbus
```


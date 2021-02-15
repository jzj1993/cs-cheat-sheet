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



## 日志管理

```bash
# 查看日志
# -u, --unit 服务名称
# -f, --follow 自动刷新
# -n, --lines 末尾行数
journalctl -f -n 100 -u NAME

# 清理日志
# https://unix.stackexchange.com/questions/139513/how-to-clear-journalctl
# Retain only the past two days:
journalctl --vacuum-time=2d
# Retain only the past 500 MB:
journalctl --vacuum-size=500M
```



```bash
# tail直接看日志文件。具体取决于journalctl和配置文件。
tail -f /var/log/journal/NAME.service.log
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

```bash
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down
```

详见 [docker.md](docker.md)



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


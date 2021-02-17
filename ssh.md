# SSH相关



## 远程登录

```bash
ssh user@host -p PORT -i IDENTITY_FILE
```



## 远程操作

```bash
# 将本机公钥添加到远程主机中
ssh user@host 'mkdir -p .ssh cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub
```



## TODO: SSH隧道

### 本地端口转发

host1通过 “连接host2的ssh隧道” 连接到host3，即

**host1(localhost)  --(ssh)-->  host2(ssh server)  ---->  host3 (dest server)**


- host1：本机，本机不能直连host3目标端口，但可以ssh连接host2。
- host2：SSH服务器，host2能不加密直连host3目标端口（例如内部网络）。
- host3：目标服务器，host2、host3可能是两个机器，也可能是同一个机器。

```bash
ssh -L [LOCAL_IP:]LOCAL_PORT:DESTINATION:DESTINATION_PORT [USER@]SSH_SERVER
```

举例：

SSH服务器上装了VNC/MySQL，端口为5901。

- 因为VNC/MySQL协议没有加密，本机直连服务器5901端口不安全；
- 服务器防火墙没开放5901端口，但是开放了SSH的22端口。

此时可以建立隧道，将本机5901通过SSH隧道转发到服务器自身的5901。之后连接本机5901端口就可以连接VNC/MySQL。

```bash
# 本机5901 - 本机SSH服务 -- 服务器22端口 -- 服务器SSH服务 -- 服务器VNC/MySQL(服务器的localhost:5901)
ssh -L 5901:localhost:5901 -N -f -l user ssh-server.com
```

### 远程端口转发

**host1(ssh server)  --(ssh)-->  host2(localhost)  ---->  host3(dest server)**

- host1：SSH远程服务器，host1不能直连host3，但可以被host2用ssh连接。
- host2：本机，本机可以不加密直接访问host3。
- host3：目标服务器，可能是本机，或本机能连接的机器。

```bash
ssh -R [REMOTE:]REMOTE_PORT:DESTINATION:DESTINATION_PORT [USER@]SSH_SERVER
```

举例：

本机3000端口部署了开发中的Web程序，没有公网IP，外网不能访问。

此时可以建立隧道，将SSH服务器的8080端口转发到本机的3000端口。之后连接SSH服务器8080端口就可以访问本机Web程序。

```bash
ssh -R 8080:localhost:3000 -N -f user@ssh-server.com
```



https://www.myfreax.com/how-to-setup-ssh-tunneling/

https://www.ruanyifeng.com/blog/2011/12/ssh_port_forwarding.html



## scp：本机/远程之间复制文件

```bash
# 远程文件路径格式 user@host:path
scp SRC_FILE DST_FILE
```

举例

```bash
scp ./LocalFile UserName@remote.host.com:/remote/path/
```




# Linux账号相关



## 启用root账户

```bash
sudo passwd root
```



## 创建新账户

创建用户，并为其创建主目录和shell 

```bash
sudo useradd -s /bin/bash -d /home/USERNAME -m -G sudo USERNAME
```



## 设置账户密码

```bash
sudo passwd USERNAME
```



## 删除账户

不能删除当前账户。

```bash
sudo userdel USERNAME
sudo userdel -r USERNAME  # 同时删除home和mail spool
```



## 列举所有账户

```bash
cat /etc/passwd
```



## 切换账户

```bash
# root
su
sudo -i
# user
su -l USERNAME

su USERNAME  # 切换到其他用户，但是不切换环境变量 
su - USERNAME  # 完整的切换到新的用户环境
```



## 登出/关机/重启

```bash
# 登出
exit
logout

# 关机
shutdown
poweroff
halt

# 重启
reboot
```



## 允许root密码登录ssh

报错：Permission denied (publickey)

```bash
sudo nano /etc/ssh/sshd_config
# PermitRootLogin yes
# PasswordAuthentication yes
```



## sudo出错

使用sudo时报错
```
xxx is not in the sudoers file. This incident will be reported.
```

```bash
# 切换到root用户下
su root
# 添加sudo文件的写权限 
chmod u+w /etc/sudoers
# 编辑sudoers文件
vim /etc/sudoers
```

在`root ALL=(ALL) ALL`一行下方，添加下列四行之一：

```bash
# 允许用户username执行sudo命令(需要输入密码)
username      ALL=(ALL) ALL

# 允许用户组usergroup里面的用户执行sudo命令(需要输入密码)
%usergroup    ALL=(ALL) ALL

# 允许用户username执行sudo命令,并且在执行的时候不输入密码
username      ALL=(ALL) NOPASSWD: ALL

# 允许用户组usergroup里面的用户执行sudo命令,并且在执行的时候不输入密码
%usergroup    ALL=(ALL) NOPASSWD: ALL
```

```bash
# 撤销sudoers文件写权限 
chmod u-w /etc/sudoers
```



## 参考

http://365vcloud.net/2017/11/26/set-linux-super-account-root-in-azure-vm/
https://www.cyberciti.biz/faq/create-a-user-account-on-ubuntu-linux/
https://www.digitalocean.com/community/questions/error-permission-denied-publickey-when-i-try-to-ssh



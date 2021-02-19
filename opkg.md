# opkg

OpenWRT系统上的软件包管理工具，和apt类似



```bash
# 添加软件源（默认位置）
vim /etc/opkg/distfeeds.conf

# 更新源
opkg update

# 安装/删除软件包
opkg install/remove PACKAGE1 PACKAGE2 ...

# 列出可更新包
opkg list-upgradable

# 更新包
opkg upgrade PACKAGE

# 更新全部
opkg list-upgradable | cut -f 1 -d ' ' | xargs opkg upgrade

# 手动下载软件
# https://downloads.openwrt.org/
# https://downloads.openwrt.org/snapshots/packages/x86_64/packages/

# 安装ipk，推荐直接在OpenWRT后台操作
opkg install FILENAME.ipk

# 列举已安装软件及版本
opkg list-installed
opkg list-installed PACKAGE

# 列举所有软件
opkg list
```


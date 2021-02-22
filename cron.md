# cron

## 命令

```bash
# 安装
apt install -y cron
# 服务
service cron start/stop/restart/status

# 编辑当前用户配置
crontab -e
# 列举配置
crontab -l
# 删除配置
crontab -r
```



## 系统全局crontab配置预设

```bash

# cron.hourly/daily/weekly/monthly
cat /etc/crontab

# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user	command
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
```



## cron语法

`Minute Hour Day Month Dayofweek command`

- Minute：分钟（0-59）
- Hour：小时（1-23）
- Day：日期（1-31）
- Month：月份（1-12）
- DayOfWeek：星期（0-6，0代表星期天）

格式

- `n`：匹配数字n
- `*`：所有数字
- `/n`：每n个单位
- `a-b`：从a到b
- `x,y,z`：匹配若干数字

示例

```bash
# 每小时第5分钟执行ls
5 * * * * ls
# 每天5:30执行ls
30 5 * * * ls
# 每月8号7:30执行ls
30 7 8 * * ls
# 每天7:50以root身份执行/etc/cron.daily目录中的所有可执行文件
50 7 * * * root run-parts /etc/cron.daily
```



https://www.jianshu.com/p/d6d8d9f7f60c
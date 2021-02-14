# MongoDB



## 远程登录配置

允许所有IP访问，开启MondoDB认证功能

```bash
sudo vim /etc/mongod.conf
```

```yaml
net:
  prot: 27017
  bindIp: 0.0.0.0    # 允许所有IP访问
security:
  authorization: enabled
```

## 创建用户

给指定的数据库创建用户

```bash
mongo
use DBNAME
db.createUser({
    user: "user",
    pwd: "pwd",
    roles: [ "readWrite" ]
})
```

## 访问数据库

```bash
mongo
use DBNAME
db.auth("user", "pwd")
```

之后数据库增删查改的语法和js调用时的语法相似


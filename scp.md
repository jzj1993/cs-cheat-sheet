# scp：本机/远程之间复制文件



```bash
# 远程文件路径格式 user@host:path
scp SRC_FILE DST_FILE
```

举例

```bash
scp ./LocalFile user@remote.host.com:/remote/path/

# 指定端口
scp -P PORT SRC_FILE DST_FILE

# 复制文件夹
scp -r SRC_PATH DST_PATH
```


# chmod / chown



```bash
# 把SRC文件权限复制给DST文件
chmod --reference=SRC DST
chown --reference=SRC DST

# 把同级src目录所有文件权限复制给当前目录对应文件
find . -exec chmod --reference=../src/{} {} \;
```


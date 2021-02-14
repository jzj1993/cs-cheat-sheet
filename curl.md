# CURL



常用参数：

- L 重定向
- o 指定下载的文件名。默认输出到stdout
- O 使用服务器上的文件名



```bash
# 下载文件，允许重定向，使用服务器上的文件名。和wget不加参数效果相似。
curl -L -O FILE_URL
```




# CURL



常用参数：

- L：允许重定向
- o：指定下载的文件名，不指定则默认输出到console
- O：使用服务器上的文件名
- s： (silently) 静默模式，不显示进度条
- X： HTTP方法，默认为GET



```bash
# 下载文件，允许重定向，使用服务器上的文件名。和wget不加参数效果相似。
curl -L -O FILE_URL

# 使用basic认证，POST请求，指定Header和Body
curl -u user:passwd -X POST -H 'Content-Type: application/json' -d '{"text":"123456"}' http://xxxx/comments
```




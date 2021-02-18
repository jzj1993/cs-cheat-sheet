# sed: stream editor



替换文件内容

```bash
# 替换字符串
sed -i 's|pattern|replacement|g' filename

# 替换字符串，使用正则表达式
sed -i 's/pattern/replacement/g' filename

# 替换包含指定字符的行
sed -i 's/.*pattern.*/This line is removed by the admin./g' filename

# 替换同时备份为filename.bak
sed -i.bak 's/pattern/replacement/g' filename
```



实际案例

```bash
# 修改docker容器中所有yaml配置文件，将db-capacity设置为3000
find /var/lib/docker/overlay2 -path "*/diff/*" -name "conf.yaml" | xargs sed -i.bak "s/.*capacity.*5000/db-capacity: 3000/g"
```



https://stackoverflow.com/questions/11245144/replace-whole-line-containing-a-string-using-sed/11245501


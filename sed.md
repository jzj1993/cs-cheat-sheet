# sed: stream editor



替换文件内容

```bash
# 替换字符串
sed -i 's/source/replacement/g' filename

# 替换包含指定字符的行
sed 's/.*TEXT_TO_BE_REPLACED.*/This line is removed by the admin./g' filename
```





https://stackoverflow.com/questions/11245144/replace-whole-line-containing-a-string-using-sed/11245501


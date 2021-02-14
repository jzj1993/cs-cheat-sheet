# tar



常用参数

- c    压缩
- x    解压
- z    有gzip属性的
- j    有bz2属性的
- v    显示所有过程
- f    **必须参数，放在最后，后面只能接档案名**



常用示例

```bash
# 打包文件夹
tar cvf output.tar src_dir # 不压缩
tar zcvf output.tar.gz src_dir # gzip压缩
tar jcvf output.tar.gz src_dir # bz2压缩

# 解压文件
tar xvf package.tar.gz # 解压到当前目录，没有压缩
tar xvf package.tar -C output_dir # 解压到指定目录，没有压缩

# 分块
tar cjf - src_dir |split -b 1024m - output.tar.bz2. # 打包，并拆分成多个不大于1024M的文件（output.tar.bz2.aa / ab / ac...）
cat logs.tar.bz2.a* | tar xj # 将打包拆分的文件解压
```


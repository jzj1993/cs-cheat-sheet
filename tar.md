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



## tar结合管道复制大量文件

源文件主机上执行tar打包，通过管道传输数据到目标主机，再通过ssh调用目标主机的tar命令实时解包。

根据文件、CPU、网络性能，可选是否开启tar压缩（参数z或j）节省数据量。

命令如下，`SRC_DIR`是源文件目录，`DST_HOST`是目标主机IP或域名，`DST_DIR`是目标文件夹。

```bash
tar cf - SRC_DIR | ssh USER@DST_HOST "cd DST_DIR && tar xvf -"
```

https://serverfault.com/questions/18125/how-to-copy-a-large-number-of-files-quickly-between-two-servers
https://serverfault.com/questions/208300/quickest-way-to-transfer-55gb-of-images-to-new-server
https://unix.stackexchange.com/questions/10026/how-can-i-best-copy-large-numbers-of-small-files-over-scp


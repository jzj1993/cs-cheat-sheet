# xargs

将结果按行/单词拆分，逐个作为文件参数调用另一个命令

示例：

```bash
# 创建demo文件
mkdir demo && cd demo
echo "A1\nB1\nC1" > A.txt
echo "A2\nB2\nC2" > B.txt

# ls输出的结果为`1.txt 2.txt`, xargs分别调用cat输出
ls | xargs cat
A1
B1
C1
A2
B2
C2

# 输出名字包含A的文件：将ls的结果输出给grep，并匹配A。
# grep只会执行一次。
ls | grep A
A.txt

# 找到文件内容包含A的行：将ls的结果逐个作为文件参数调用grep，并匹配文件内容中的A。
# 每个结果会执行一次grep。
ls | xargs grep A
A.txt:A1
B.txt:A2

# 找到名字包含A的文件，输出其内容中包含B的行
find . -name "*A*" | xargs grep B
B1
```



```bash
# 创建测试文件
mkdir test && cd test
for i in {1..9}; echo $i-content > $i

# 设置占位符
ls | xargs -I {} echo {}

# 执行多个命令
ls | xargs -I {} bash -c "echo {} && cat {}"
```


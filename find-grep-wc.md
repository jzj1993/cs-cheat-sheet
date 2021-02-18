# find / grep / wc



## find: 递归搜索文件

用法

```bash
find path1 path2 ... [EXPRESSIONS]
```

常用参数

```bash
-name  # 指定名字，支持通配符
-path  # 指定路径，支持通配符
```

举例

```bash
find . -name "*abc*"  # 搜索当前目录和子目录，找到路径包含abc的文件
find . -name "*abc*" -path "*/diff/*" # 搜索带有diff的目录下名字包含abc的文件
```



## grep: 搜索文件内容

用法

```bash
grep [OPTIONS] "pattern" file1 file2 ...
```

常用参数

```bash
-i  # 不区分大小写。默认区分大小写。
-R  # 递归搜索
```

举例

```bash
grep "abc" 1.txt    # 当前目录1.txt文件中搜索"abc"
grep "abc" *        # 当前目录文件内容中搜索"abc"
grep "abc" *.js     # 当前目录js文件内容中搜索"abc"
grep "abc" * .*     # 当前目录文件内容中搜索"abc"，包括隐藏文件，这里使用了两个通配符

grep -i "a" *       # 当前目录文件内容中搜索"a"或"A"

grep -R "abc" **/*.js  # 当前目录和子目录js文件内容中搜索"abc"
```



## wc: 统计数量（行/单词/字符/字节）

用法

```bash
wc [-clmw] [file ...]
```

常用参数

```bash
-l  # 统计行数 lines
-w  # 统计单词数 words
```

举例

```bash
wc -l *.js    # 统计当前目录js文件行数，输出行数和文件名
cat README.md | wc -l # 统计文件README.md行数，输出行数
echo "a b c\nd e f" | wc -l # 统计行数，输出2
echo "a b c\nd e f" | wc -w # 统计单词数，输出6
```





## 应用：统计文件/文件夹数量

```bash
# 统计文件夹下文件数量
ls -l | grep "^-" | wc -l

# 统计文件夹下目录数量
ls -l | grep "^ｄ" | wc -l

# 统计文件夹下文件数量，含子文件夹
ls -lR | grep "^-" | wc -l
```



## 应用：统计代码行数

https://www.paincker.com/shell-count-code-lines

### 用grep匹配文件内容

```bash
# 这条命令会把index.js内容全部输出来，因为每行都匹配
grep "" index.js

# 这条命令会把index.js非空行的内容输出来，"^$"匹配所有空行，-v表示翻转匹配，所以每个非空行被匹配并输出
grep -v "^$" index.js

# 这条命令会把当前目录和子目录下所有js文件非空行的内容输出来，-R表示递归子目录
grep -R -v "^$" **/*.js
```

### 用wc统计行数

```bash
# 通过管道将内容传递给wc，统计js代码行数
grep -R -v "^$" **/*.js | wc -l
```

### 使用find过滤文件

```bash
# 使用find列出所有扩展名为m和h的文件，通过管道和xargs传递给grep，然后统计代码行数
find . -name "*.m" -or -name "*.h" | xargs grep -v "^$" | wc -l
```

xargs的作用：

- xargs把find输出的内容转换为**命令行参数**传递给grep。例如find输出的内容是`main.js \n test.js`，则find输出的每一行（即每个文件）都会调用一次grep，也就是`grep main.js`, `grep test.js`，输出每个文件中匹配的内容，最后由wc计算总行数。
- 如果不用xargs，grep会把find输出的文件列表当做要搜索的内容，最后统计的是文件列表的行数（只会调用一次grep），也就是文件的个数。

### 直接用find统计行数

直接用find也可以统计行数，但是只能统计文件中所有内容（包括空行）的行数，没有用grep那么灵活。

```bash
# 统计每个文件的行数和总行数，包含空行
find . -name "*.js" | xargs wc -l

# 统计每个文件的行数和总行数，包含空行，最后按行数排序
find . -name "*.js" | xargs wc -l | sort -n
```
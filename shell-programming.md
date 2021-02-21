# Shell 编程



## Shebang Line

```bash
#!/usr/bin/env bash
```



## 控制流

### for

```bash
## 写法

# 单行
for i in 1 2 3; echo $i

# do / done
for i in 1 2 3
do
	echo $i
done

# do / done
for i in 1 2 3; do
	echo $i
done

## 循环内容

# 循环数列
for i in $(seq 1 3); echo $i
for i in {1..3}; echo $i
for i in {0..-10}; echo $i

# 从ls返回结果循环
for i in `ls`; echo $i

# 从数组循环
LIST=(a b); for i in $LIST; echo $i

# 按单词循环，使用IFS或shwordsplit
LIST="a b"; for i in ${=LIST}; echo $i
setopt shwordsplit; LIST="a b"; for i in $LIST; echo $i; unsetopt shwordsplit
```

https://stackoverflow.com/questions/23157613/how-to-iterate-through-string-one-word-at-a-time-in-zsh



## 获取function参数

```bash
function func() {
	local ARG1=$1
	local ARG2=$2
	echo $ARG1 $ARG2
}
func "hello" "world"
func -f test

# 运行结果
hello world
-f test
```



## 捕获function返回值

```bash
function func() {
    # stdout输出的内容为function的返回值
    echo "hello1"
    echo "hello2"
    # 如果需要正常echo输出，使用这种格式
    echo "hello3" >&2
    # return可以返回一个整数值(result code)，用$?获得
    return 5
}
A=`func` # or A=$(func)
echo $?
echo $A

# 运行结果
# echo from func
hello3
# $?: result code
5
# A: return value
hello1
hello2
```



## 读取变量

```bash
DEFAULT_VALUE='Tom'
echo "Enter your name [${DEFAULT_VALUE}]:"
read name
name=${name:-$DEFAULT_VALUE}
echo "Your name is ${name}"
```



## 数学计算

```bash
# 操作符之间不能有空格
let A=10+10
echo $A          # 20
echo $[15/10]    # 1
echo $((15%10))  # 5
# 操作符之间需要有空格
echo `expr 10 + 10`  # 20
echo `expr 10+10`    # 10+10
```



## 判断文件是否存在

```bash
# exist
if [ -f /etc/file ]; then
	echo "File exists."
fi

# not exist
if [ ! -f /etc/file ]; then
	echo "File does not exist."
fi

# simple statement
[ -f /etc/resolv.conf ] && echo "File exists."

# multiple file
if [ -f /etc/resolv.conf -a -f /etc/hosts ]; then
    echo "Both files exist."
fi
```

https://linuxize.com/post/bash-check-if-file-exists/



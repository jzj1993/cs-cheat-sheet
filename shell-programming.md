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
	echo $1           # 1st arg
	echo $2           # 2nd arg
	echo ${@: 2: 2}   # from 2nd, length 2
	echo ${@: 2}      # from 2nd to last
	echo ${@: -2: 1}  # 2nd from end, length 1
}
func "arg1" "arg2" "arg3" "arg4"

# 运行结果
arg1
arg2
arg2 arg3
arg2 arg3 arg4
arg3
```



## 捕获function返回值

```bash
function func() {
    # stdout输出的内容为function的返回值
    echo "result 1"
    echo "result 2"
    # 如果需要正常echo输出，可以输出到stderr
    echo "message" >&2
    # return可以返回一个整数值(result code)，用$?获得
    return 5
}
RESULT=`func` # or A=$(func)
echo $?       # 获取前一个命令的result code
echo $RESULT  # 输出result

# 运行结果
message
5
result 1
result 2
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



## 获取当前脚本和目录路劲

```bash
__script=${BASH_SOURCE[0]}
__dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
echo $__script
echo $__dir
```

https://stackoverflow.com/questions/35006457/choosing-between-0-and-bash-source

https://stackoverflow.com/questions/38978650/run-a-script-in-the-same-directory-as-the-current-script



## 判断文件是否存在

```bash
# 注意-f判断的是文件，文件夹要用-d
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



## 后台运行与静默运行

基础解释

```bash
# 前台运行。stdout和stderr都会输出到console。会阻塞后面的命令。
COMMAND

# 后台运行。stdout和stderr都会输出到console。不会阻塞后面的命令。
COMMAND &

# 忽略HUP信号，同时把COMMAND的stdout和stderr输出到nohup.out。
# 如果设置了huponexit，shell结束时会发送SIGHUP，COMMAND会终止，使用nohup则忽略HUP信号。
# 注意，nohup自身的output还是会输出到console。
nohup COMMAND

# 把stdout重定向到FILE
COMMAND > FILE

# 把stdout重定向到/dev/null，也就是忽略输出
COMMAND > /dev/null

# 把stderr重定向到stdout
COMMAND 2>&1
```

实际案例

```bash
# 前台静默运行，阻塞后续命令
COMMAND >/dev/null 2>&1

# 后台运行，输出stdout和stderr到nohup.out
nohup COMMAND &

# 后台静默运行
nohup COMMAND >/dev/null 2>&1 &
```

实验

```bash
$ mkdir test && cd test

# 正常运行echo
$ echo hello
hello

# 后台运行echo。stdout仍然会输出信息。且进程启动和结束时会有log显示。
$ echo hello &
[1] 9490
hello
[1]  + 9490 done       echo hello

# 后台运行ls，随后前台运行echo。可以看到ls不会阻塞echo，hello提前被输出。
$ ls / & \
$ > echo hello
[1] 9601
hello
bin   dev  home  lib    lib64   lost+found  mnt  proc  run   snap  sys  usr
boot  etc  init  lib32  libx32  media       opt  root  sbin  srv   tmp  var
[1]  + 9601 done       ls --color=tty /

# nohup运行echo。默认会把stdout输出到nohup.out文件。
$ nohup echo hello
nohup: ignoring input and appending output to 'nohup.out'
$ cat nohup.out
hello

# 运行cat并重定向stdout到out文件。stderr仍然输出到了console。
$ cat non-existent-file > out
cat: non-existent-file: No such file or directory

# 运行cat并重定向stdout和stderr到out文件。stderr也被输出到了out文件。
$ cat non-existent-file > out 2>&1
# or: cat non-existent-file 1>out 2>out
# or: cat non-existent-file >out 2>out
$ cat out
cat: non-existent-file: No such file or directory
```



https://stackoverflow.com/questions/15595374/whats-the-difference-between-nohup-and-ampersand

https://stackoverflow.com/questions/818255/in-the-shell-what-does-21-mean

https://unix.stackexchange.com/questions/4126/what-is-the-exact-difference-between-a-terminal-a-shell-a-tty-and-a-con
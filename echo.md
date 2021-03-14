# echo



## echo换行控制

示例1

```bash
# -n表示末尾不自动换行，-e允许转义符号，\r表示回退到行首，覆盖当前行内容
echo -ne "abc\rx"

# 运行结果
xbc
```

示例2

```bash
for i in {1..20}; do
	# 末尾加空格，确保可以覆盖原先所有可见字符
	echo -ne "\rcalculate $i "
	if [ $(( i % 5 )) -eq 0 ]; then
	    # 增加一个echo换行，否则会追加显示到hello $i的末尾
	    echo
	    echo "$i can be divided by 5"
	fi
	sleep 0.5
done

# 运行结果（不能被5整除的echo输出，都被覆盖掉了）
calculate 5
5 can be divided by 5
calculate 10
10 can be divided by 5
calculate 15
15 can be divided by 5
calculate 20
20 can be divided by 5
```


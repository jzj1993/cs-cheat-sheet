# jq



```bash
apt install -y jq
```



```bash
# 选取元素
echo '{"a":1,"b":{"c":2}}' | jq -r '.a'
1
echo '{"a":1,"b":{"c":2}}' | jq -r '.b.c'
2

# 数组长度
echo '[1,2,3,4]' | jq -r 'length'
4

# 选择数组元素
echo '["a","b","c"]' | jq -r '.[0:2]'
[
  "a",
  "b"
]

# pipe到下一个filter
echo '{"a":[3,4,5,6]}' | jq -r '.a | length'
4
```



GitHub https://github.com/stedolan/jq

Playground https://jqplay.org/
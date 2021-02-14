# Shell 编程



## Shebang Line

```bash
#!/usr/bin/env bash
```



## Test File Exists

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
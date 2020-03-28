# 时区设置

## 启动后修改

```bash
# 查看是否是链接的方式
$ ls -al /etc/localtime
xxxx /etc/localtime -> /usr/share/zoneinfo/Asia/Shanghai

# 如果不是，链接到指定时区文件，f 强制覆盖
$ ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 或 复制时区文件
# cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 使用宿主机时区
$ docker cp /etc/localtime container_id:/etc/localtime
```

## 启动时设置

```bash
# 设置时区环境
$ docker run -d \
  -e TZ="Asia/Shanghai" \
  ...


# 挂载宿主机时区
$ docker run -d \
  -v /etc/localtime:/etc/localtime \
  ...
```





## Read More

- [主机、Docker时间与时区设置总结](https://www.cnblogs.com/zengming/p/10190317.html)
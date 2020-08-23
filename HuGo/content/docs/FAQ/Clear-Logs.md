# 清理 Docker 容器日志

## 查看日志

``` bash
$ docker logs -f --tail=10 <container name>
```



## 清除日志文件

```bash
# MAC Docker tty
# $ screen ~/Library/Containers/com.docker.docker/Data/vms/0/tty

# 日志文件路径
$ docker inspect -f '{{.LogPath}}' <container name>
# 或
$ docker inspect <container name> | grep -2 Log
"LogPath": "/var/lib/docker/containers/xxx/xxx-json.log",

# 查看容器列表
$ ls -al /var/lib/docker/containers

# 找到日志文件并计算大小
$ find /var/lib/docker/containers/ -size +500k -name *-json.log | xargs du -sh

# 清除日志文件
$ echo "" > /var/lib/docker/containers/xxxxxxx/xxxxxxx-json.log
```



## 设置日志文件大小

### 全局设置

```bash
$ vim /etc/docker/daemon.json

{
  "log-driver":"json-file",
  "log-opts": {"max-size":"500m", "max-file":"3"}
}
```

### docker-compose

``` yaml
services:
  nginx: 
    ...
    logging: 
      driver: "json-file"
      options: 
        max-size: "500m"
```



## Read More

- ！[Configure logging drivers](https://docs.docker.com/config/containers/logging/configure/)
- [Compose file reference # logging](https://docs.docker.com/compose/compose-file/#logging)
- [Docker 容器日志查看与清理](https://my.oschina.net/u/2289161/blog/1931390)
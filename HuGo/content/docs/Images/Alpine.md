# alpine 镜像



## 进入 alpine 

```bash
$ docker run -it --rm alpine:3.12.3

# 别名
/ $ alias ll='ls -l'
```





## apk 包管理



### 镜像加速

```bash
$ cat /etc/apk/repositories
http://dl-cdn.alpinelinux.org/alpine/v3.12/main
http://dl-cdn.alpinelinux.org/alpine/v3.12/community

# 改为国内镜像地址
$ sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories

# ❤ 本地缓存地址
$ ls -al /var/cache/apk/
```



### 常用命令

```bash
# 帮助
$ apk --help
# 子命令帮助
$ apk add --help

# 安装
$ apk add curl

# 卸载
$ apk del curl
```



## Dockerfile

```dockerfile
FROM alpine:3.12.3

# 安装 curl
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories
RUN apk add curl && rm -rf /var/cache/apk/*

#
WORKDIR /opt

...
```





## Read More

- Docker Hub [Alpine](https://hub.docker.com/_/alpine)
- [alpine linux 官网](https://alpinelinux.org/)
  - Package： https://pkgs.alpinelinux.org/packages
  - 镜像： https://mirrors.alpinelinux.org/ --> 搜索 China


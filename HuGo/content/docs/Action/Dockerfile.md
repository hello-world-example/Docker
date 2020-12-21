# Dockerfile



## 指令

- 指令不区分大小写，但是**建议使用 大写，便于跟参数区分开**
- 按顺序执行 Dockerfile 中的指令
- 必须以 `FROM` 开头，`ARG` 除外
- 为了兼容行，会忽略 `#` 和 `指令` 前面的空格，但是 **不建议 注释 和 指令前面加空格**



### ARG

- 接收  `docker build --build-arg <varname>=<value>` 参数
- 唯一已和可以出现在 `FROM` 指令前的指令

```dockerfile
ARG JDK_VERSION=8u102
FROM openjdk:${JDK_VERSION}
```



### FROM

指定基础镜像

| 类型     | 常用基础镜像  | 简介                        |                                   |
| -------- | ------------- | --------------------------- | --------------------------------- |
| 操作系统 | busybox       | 超级简化版嵌入式 Linux 系统 |                                   |
| 操作系统 | alpine        |                             |                                   |
| 操作系统 | centos        |                             |                                   |
| 操作系统 | ubuntu        |                             |                                   |
|          |               |                             |                                   |
| Java     | openjdk:8u102 | JDK                         | https://hub.docker.com/_/openjdk/ |



### ~~MAINTAINER (deprecated)~~

- 设置生成镜像的作者信息

- `LABEL` 指令更灵活，应该使用它，如：`maintainer="Kail.xyz"`
- 可以通过 `docker inspect` 看到



### LABEL

```dockerfile
LABEL "com.example.vendor"="ACME Incorporated"
LABEL com.example.label-with-value="foo"
LABEL version="1.0"
LABEL multi.label1="value1" multi.label2="value2" other="value3"
```

通过 `docker image inspect myimage` 查看镜像 `label` 信息



### ENV

指定环境变量

```dockerfile
ENV abc=hello
ENV abc=bye def=$abc
ENV ghi=$abc

WORKDIR ${abc}   # WORKDIR /hello
```



### EXPOSE

- **声明容器应该打开的端口，并没有实际上将它打开**
- 作用是 构建映像的人 和 运行容器的人 之间的一种文档形式
- 发布哪些端口，要在运行容器时 使用 `docker run -p` 标志发布和映射一个或多个端口

```dockerfile
# 默认 TCP
EXPOSE 80

# 指定类型
EXPOSE 80/tcp
EXPOSE 80/udp
```



### WORKDIR

- 跟随它的任何 RUN、CMD、ENTRYPOINT、COPY 和 ADD 指令设置工作目录
- WORKDIR 指令可以在多次使用
- 如果提供了相对路径，则该路径将相对于上一个 WORKDIR 指令的路径

```dockerfile
WORKDIR /a
WORKDIR b
WORKDIR c
# /a/b/c
RUN pwd 
```



### USER

指定运行时的用户

```dockerfile
USER <user>[:<group>]

USER <UID>[:<GID>]
```



### RUN

- 在构建镜像时，对镜像内的内容进行修改

```dockerfile
# json 格式，必须使用双引号
RUN ["/bin/bash", "-c", "echo hello"]


RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME'
RUN /bin/bash -c 'source $HOME/.bashrc; \
echo $HOME'

RUN --no-cache /bin/bash -c 'echo "" > /opt/logs/demo.log'
```



### ADD

-  `<src>` 路径必须在构建的上下文中，不能是 `ADD ../something /something`，因为 `docker build` 的第一步是吧上下文和子目录发送给 Docker 守护进程
-  `<src>` 如果是本地的 `tar` 格式的压缩文件( `gzip`, `bzip2` or `xz`)，会自动解压，识别不是通过文件名，而是文件内容
- 如果 `<dest>` 不是以 `/` 结尾，则代表是普通文件，`<src>`  内容将写入 `<dest>` 
- 如果 `<dest>` 不存在，会自动创建

```dockerfile
ADD hom?.txt /mydir/

# <WORKDIR>/relativeDir/
ADD test.txt relativeDir/

# 如果文件名是 arr[0].txt
ADD arr[[]0].txt /mydir/

# 指定权限
ADD --chown=55:mygroup files* /somedir/
ADD --chown=bin files* /somedir/
ADD --chown=1 files* /somedir/
ADD --chown=10:11 files* /somedir/
```



### COPY

- 格式同 `ADD`
- 单纯的 复制，不会像 `ADD` 那样有 自动解压、远程下载 等功能



### CMD

- 只能有一条 `CMD` 指令，如果列出多个命令，则只有最后一个命令生效
- 主要用途是**为容器提供默认值**

```dockerfile
CMD ["可执行文件或命令","param1","param2"] 

# 为 ENTRYPOINT 提供默认参数
CMD ["param1","param2"] 

# shell 命令
CMD command param1 param2 
```





### ENTRYPOINT

- ENTRYPOINT 中的参数始终会被使用，而 CMD 的额外参数可以在容器启动时动态替换掉

```dockerfile
ENTRYPOINT ["/bin/echo", "Hello"]  

docker run -it [image] 
Hello

docker run -it [image] CloudMan
Hello CloudMan


# ---------------------------
ENTRYPOINT ["/bin/echo", "Hello"]  
CMD ["world"]

docker run -it [image] 
Hello world

docker run -it [image] CloudMan
Hello CloudMan

```



### SHELL

- 用于重写默认 `Shell`
  - Linux 默认： `["/bin/sh", "-c"]`
  - Windows 默认： `["cmd", "/S", "/C"]`

```dockerfile
FROM microsoft/windowsservercore

# Executed as cmd /S /C echo default
RUN echo default

# Executed as cmd /S /C powershell -command Write-Host default
RUN powershell -command Write-Host default

# Executed as powershell -command Write-Host hello
SHELL ["powershell", "-command"]
RUN Write-Host hello

# Executed as cmd /S /C echo hello
SHELL ["cmd", "/S", "/C"]
RUN echo hello
```





### ONBUILD 模板镜像

- `ONBUILD` 指令对 当前构建的镜像 A 不会产生影响
- 当镜像 B 基于 镜像 A 构建时，这时镜像 A Dockerfile 中的 `ONBUILD` 就会生效
- 在构建 镜像B 的过程中，首先会执行 `ONBUILD` 指令指定的指令，然后才会执行其它指令
- 再利用 镜像B 构造新的镜像时，`ONBUILD` 指令就无效了，即：**在构建子镜像中执行，对孙子镜像构建无效**

### VOLUME

- 可以在镜像中创建挂载点，要通过该镜像创建的容器都有了挂载点
- VOLUME 指令创建的挂载点，无法指定主机上对应的目录，是自动生成的
- 通过 `docker inspect` 的 `Mounts.Source` 查看宿主机上生成的路径
- `docker run`
  - `-v /opt/data/myimage:/data` 指定宿主机的挂载点
  - `-v /data` 自动在宿主机上生成挂载点
  - `--volumes-from 容器1` 与 `容器1` 共享挂载点

```dockerfile
VOLUME ["/data1","/data2"]
```



### HEALTHCHECK 健康检查

- `HEALTHCHECK NONE` 禁用基础镜像的健康检查
- 格式 `HEALTHCHECK [OPTIONS] CMD command`
  - `--interval=DURATION` (default: `30s`)
  - `--timeout=DURATION` (default: `30s`)
  - `--start-period=DURATION` (default: `0s`)
  - `--retries=N` (default: `3`)
- 状态
  - `0`: `success` 正常
  - `1`: `unhealthy` 不正常
  - ~~`2`: `reserved` 不要使用~~

```dockerfile
HEALTHCHECK --interval=5m --timeout=3s CMD curl -f http://localhost/ || exit 1
```



## 构建方法

```bash
$ docker build .

# 指定 Dockerfile
$ docker build -f /path/Dockerfile .

# 指定镜像名
$ docker build -t demo/kail .
# 多个镜像
$ docker build -t demo/kail:1.0.0 -t demo/kail:latest .
```

## .dockerignore 

```bash
# comment
*/temp*
*/*/temp*
temp?

# 排除 md，但不排除 README.md
*.md
!README.md
```





## 常见问题

###  `RUN`、`CMD`、`ENTRYPOINT` 的区别

- `RUN` 是在 Build 时运行的，在构建镜像时对镜像内容进行修改可以用 `RUN`，先于 `CMD` 和 `ENTRYPOINT`
- `ENTRYPOINT` 和 `CMD` 的不同点在于执行 `docker run` 时参数传递方式
  -  `CMD` 指定的命令可以被 `docker run` 传递的命令覆盖，相当于默认指令
  - `ENTRYPOINT` 会把容器名后面的所有内容都当成参数传递给其指定的命令，不会被覆盖
- `ENTRYPOINT` 个Dockerfile 只能出现一个，如果有多个，最后一个生效



> - [Dockerfile里指定执行命令用ENTRYPOING和用CMD有何不同？](https://segmentfault.com/q/1010000000417103)
> - [Understand how CMD and ENTRYPOINT interact](https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact)



### `ADD`、`COPY` 的区别

- `ADD` 和 `COPY` 的作用都是把文件复制到镜像中
- `COPY` 的功能比较单纯，仅仅就是字面意思的复制
- `ADD` 相对来说功能多一点，如果复制的是压缩包，会自动解压，如果是一个URL，会自动下载文件到镜像中
- 选择的话**建议使用 `COPY`，因为 `ADD` 相对来说有一定的歧义**
  - 例如你想复制一个压缩包到Docker容器，但是压缩格式繁多，结果有可能解压有可能不解压
- `ADD` 的下载功能完全可以通过 `curl` 或者 `wget` 来搞定



> [Dockerfile: ADD vs COPY](http://blog.csdn.net/liukuan73/article/details/52936045)



### 指令格式：Shell、Exec

- Shell 如： 
  - `RUN apt-get install python3`  
  - `CMD echo "Hello world"`  
  - `ENTRYPOINT echo "Hello world"` 
  - **底层调用**： `/bin/sh -c <command>`
- Exec 如：
  - `RUN ["apt-get", "install", "python3"]`  
  - `CMD ["/bin/echo", "Hello world"]`  
  - `ENTRYPOINT ["/bin/echo", "Hello world"]`
  - **不会被 Shell 解析**
- 区别
  - Exec 无法解析环境变量
  - CMD 和 ENTRYPOINT 推荐使用 Exec 格式
  - RUN 则两种格式都可以



## Read More

- [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
- [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [RUN vs CMD vs ENTRYPOINT - 每天5分钟玩转 Docker 容器技术](https://www.cnblogs.com/CloudMan6/p/6875834.html)
- [虚拟化和Doker技术](https://www.cnblogs.com/51kata/category/789766.html)
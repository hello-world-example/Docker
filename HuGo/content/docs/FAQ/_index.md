# 常见问题



## 镜像加速器

登陆 [阿里云](https://account.aliyun.com/login/login.htm) > 使用 [镜像加速器](https://cr.console.aliyun.com/cn-beijing/instances/mirrors) （内置教程）



## 容器启动后闪退

>  Docker 容器后台运行，就必须有一个前台进程

```bash
docker run -d centos /bin/bash -c "while true; do echo noting; sleep 1; done"
```



## root 用户进行容器

```bash
docker exec -it --user root <container id> /bin/bash
```




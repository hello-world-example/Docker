# 常见问题



## 容器启动后闪退

>  Docker 容器后台运行，就必须有一个前台进程

`docker run -d centos /bin/bash -c "while true; do echo noting; sleep 1; done"`


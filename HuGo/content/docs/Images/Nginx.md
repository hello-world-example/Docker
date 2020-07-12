# Nginx




## 目录结构

``` bash
.
├── Dockerfile
├── conf.d
│   └── default.conf
└── docker-compose.yml
```



## Dockerfile

``` Dockerfile
FROM nginx
MAINTAINER Mr.Kail

# WORKDIR /
EXPOSE 80

# 删除容器内 Nginx 配置文件
RUN rm /etc/nginx/conf.d/default.conf
# 把配置文件拷贝到容器内
ADD ./conf.d/default.conf /etc/nginx/conf.d/default.conf
# 启动 nginx
RUN nginx

# docker build -t kail/nginx .
```



## docker-compose.yml

``` yml
version: '2'
services:
  kail:
    build: .
    restart: always

    # 端口是 80
    ports:
     - "80:80"
    
    # 
    volumes:
     - "/opt/data/docker-volume/nginx/html:/tmp/html"
  
# docker-compose up -d
# docker run -d -p 80:80 -v /opt/data/docker-volume/nginx/html:/tmp/html --name nginx_kail kail/nginx
# docker run -d -p 80:80 -v /opt/data/docker-volume/nginx/html:/tmp/html --name nginx_kail nginx_kail
```



## conf.d/default.conf

``` nginx
server {
    listen       80;
    server_name  localhost;

    location / {
        root   /tmp/html;
        index  index.html index.htm;
    }
}
```


`docker pull nginx` 拉下来的镜像，启动之后配置文件在 `/etc/nginx/nginx.conf`，其会 `include` `/etc/nginx/conf.d/` 下的 `*.conf` 配置，Dockerfile 中拷贝了自定义的 `default.conf` 到该目录下。



 `/etc/nginx/nginx.conf` 内容摘录如下：

``` nginx

...

http {
    include       /etc/nginx/mime.types;
    
    ...

    include /etc/nginx/conf.d/*.conf;
}

```



## 运行

``` bash
docker-compose up -d
```

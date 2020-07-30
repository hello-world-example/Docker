# docker-compose.yml





## 文件结构和示例

> - [YAML、YML在线编辑(校验)器](http://www.bejson.com/validators/yaml_editor/)
> - [Compose file structure and examples](https://docs.docker.com/compose/compose-file/#compose-file-structure-and-examples)

```yaml

version: "3.8"
services:
  redis:
    # 镜像
    image: redis:alpine
    # "no": 默认策略
    # always: 不管什么状态都重启
    # on-failure: 容器以非 0 状态退出时重启
    # unless-stopped: 不管什么退出状态都重启，如果容器之前已经为停止状态，不尝试启动
    restart: always
    # 端口映射，同 "6379:6379"
    ports:
      - "6379"
    # 指定 DNS
    dns:
      - 8.8.8.8
      - 9.9.9.9
    # /etc/hosts，--add-host
    extra_hosts:
      - "somehost:162.242.195.82"
      - "otherhost:50.31.209.229"
    networks:
      - frontend
    env_file: .env
    environment:
      - RACK_ENV=development
      - SHOW=true
      - SESSION_SECRET
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure

  db:
    image: postgres:9.4
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend
    deploy:
      placement:
        max_replicas_per_node: 1
        constraints:
          - "node.role==manager"

  vote:
    image: dockersamples/examplevotingapp_vote:before
    ports:
      - "5000:80"
    networks:
      - frontend
    # 依赖 redis
    depends_on:
      - redis
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
      restart_policy:
        condition: on-failure

  result:
    image: dockersamples/examplevotingapp_result:before
    ports:
      - "5001:80"
    networks:
      - backend
    depends_on:
      - db
    deploy:
      replicas: 1
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure

  worker:
    image: dockersamples/examplevotingapp_worker
    networks:
      - frontend
      - backend
    deploy:
      mode: replicated
      replicas: 1
      labels: [APP=VOTING]
      restart_policy:
        condition: on-failure
        delay: 10s
        max_attempts: 3
        window: 120s
      placement:
        constraints:
          - "node.role==manager"

  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "8080:8080"
    stop_grace_period: 1m30s
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints:
          - "node.role==manager"

networks:
  frontend:
  backend:

volumes:
  db-data:
```





## 与 Docker Engine 兼容性

> - [Compatibility matrix](https://docs.docker.com/compose/compose-file/compose-versioning/#compatibility-matrix)
>
> - compose 2.0 和 3.0 的文件格式没有区别，但是一些选项被删除
>   - https://docs.docker.com/compose/compose-file/compose-versioning/#version-2x-to-3x
> - compose 1.0 和 2.0 的文件格式有区别，如果要升级需要：
>   - 整个文件增加一个缩进，并在顶部增加 `services:`
>   - 在第一行增加 `version: '2'` 版本号
>   - https://docs.docker.com/compose/compose-file/compose-versioning/#version-1-to-2x

| **Compose file format** | **Docker Engine release** |
| :---------------------- | :------------------------ |
| 3.8                     | 19.03.0+                  |
| 3.7                     | 18.06.0+                  |
| 3.6                     | 18.02.0+                  |
| 3.5                     | 17.12.0+                  |
| 3.4                     | 17.09.0+                  |
| 3.3                     | 17.06.0+                  |
| 3.2                     | 17.04.0+                  |
| 3.1                     | 1.13.1+                   |
| 3.0                     | 1.13.0+                   |
| 2.4                     | 17.12.0+                  |
| 2.3                     | 17.06.0+                  |
| 2.2                     | 1.13.0+                   |
| 2.1                     | 1.12.0+                   |
| 2.0                     | 1.10.0+                   |
| 1.0                     | 1.9.1.+                   |



## Read More

- [Compose file version 3 reference](https://docs.docker.com/compose/compose-file/)
- [Compose file version 2 reference](https://docs.docker.com/compose/compose-file/compose-file-v2/)
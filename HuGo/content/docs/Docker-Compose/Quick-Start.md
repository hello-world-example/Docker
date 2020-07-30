# docker-compose 快速开始



## 安装

```bash
# 下载 docker-compose 文件
curl -L https://github.com/docker/compose/releases/download/1.26.2/docker-compose-`uname -s`-`uname -m` \
  > /usr/local/bin/docker-compose
  
# 授予可执行权限
chmod u+x /usr/local/bin/docker-compose

# 查看版本
docker-compose version
```





## docker-compose.yml

### 模板

```yaml
version: '2.0'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```







## 命令

```bash
# 查看帮助
$ docker-compose help
Define and run multi-container applications with Docker.

Usage:
  docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
  docker-compose -h|--help

Options:
  ...

Command:
  ...
```

### Options

| Option | 简介 |
| :--: | ---- |
| -f, --file FILE | Specify an alternate compose file (default: docker-compose.yml) |
| -p, --project-name NAME | Specify an alternate project name (default: directory name) |
| --verbose | Show more output |
| --log-level LEVEL | Set log level (DEBUG, INFO, WARNING, ERROR, CRITICAL) |
| --no-ansi | Do not print ANSI control characters |
| -v, --version | Print version and exit |
| -H, --host HOST | Daemon socket to connect to |
| --tls | Use TLS; implied by --tlsverify |
| --tlscacert CA_PATH | Trust certs signed only by this CA |
| --tlscert CLIENT_CERT_PATH | Path to TLS certificate file |
| --tlskey TLS_KEY_PATH | Path to TLS key file |
| --tlsverify | Use TLS and verify the remote |
| --skip-hostname-check | Don't check the daemon's hostname against the name specified in the client certificate |
| --project-directory PATH | Specify an alternate working directory (default: the path of the Compose file) |
| --compatibility | If set, Compose will attempt to convert keys in v3 files to their non-Swarm equivalent |
| --env-file PATH | Specify an alternate environment file |

### Commands

| Command | 简介 |
| :--: | ---- |
| build | Build or rebuild services |
| config | Validate and view the Compose file |
| create | Create services |
| down | Stop and remove containers, networks, images, and volumes |
| events | Receive real time events from containers |
| exec | Execute a command in a running container |
| help | Get help on a command |
| images | List images |
| kill | Kill containers |
| logs | View output from containers |
| pause | Pause services |
| port | Print the public port for a port binding |
| ps | List containers |
| pull | Pull service images |
| push | Push service images |
| restart | Restart services |
| rm | Remove stopped containers |
| run | Run a one-off command |
| scale | Set number of containers for a service |
| start | Start services |
| stop | Stop services |
| top | Display the running processes |
| unpause | Unpause services |
| up | Create and start containers |
| version | Show the Docker-Compose version information|



## Read More

- [Get started with Docker Compose](https://docs.docker.com/compose/gettingstarted/)



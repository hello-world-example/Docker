# docker-entrypoint.sh 模板



## 官方示例

> - [【Redis 6.0】docker-entrypoint.sh](https://github.com/docker-library/redis/blob/master/6.0/docker-entrypoint.sh)
> - [【MySQL 5.7 】docker-entrypoint.sh](https://github.com/docker-library/mysql/blob/master/5.7/docker-entrypoint.sh)



## 示例代码

```bash
#!/bin/sh

set -eo pipefail
shopt -s nullglob

# ------------------
echo do something
# ------------------

echo "start exec '$@'"
exec "$@"
```



## 含义

### set -e

- 如果任何语句支持结果不是 0（出错），则应该退出
- 如果要增加可读性, 可以使用 `set -o errexit`



### set -o pipefail

- 与 `set -e` 类似，作用域是管道
- 脚本中的管道，如果前面的命令执行出了问题， 应该立即退出
- 可与 `set -e` 结合 `set -eo pipefail`
- `set -o` 查看所有可设置的选项



### shopt -s nullglob

- 在使用 Linux 中的通配符时 `* ?` 时，没有匹配到时执行去掉通配符的内容，如 在空文件夹下 执行 `echo /tmp/123/*`：
  - 未设置时输出 输出 `/tmp/123/*`
  - 设置后输出 空字符串
- `shopt -p` 列出所有可设置的选项



### exec "$@"

- 在执行完 `docker-entrypoint.sh` 的内容后，执行用户自定义脚本



### 其他

> - [Shell 变量与参数](/Linux/docs/100_Shell/010.var/)
> - [Shell 字符串处理](http://kail.xyz/Linux/docs/100_Shell/011.var_string/)



## 巨人的肩膀

- [编写 docker-entrypoint.sh 入口文件](https://docs.lvrui.io/2017/06/09/%E7%BC%96%E5%86%99docker-entrypoint-sh%E5%85%A5%E5%8F%A3%E6%96%87%E4%BB%B6/)
- [Bash 为何要发明 shopt 命令](https://www.cnblogs.com/ziyunfei/p/4913758.html)
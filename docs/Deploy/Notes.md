
## Docker注意事项

Docker 日志如果不手动限制会将磁盘空间撑爆, 解决方案有两种

- 在 compose 中手动声明

```yml
db:
  image: mysql:latest
  logging:
    driver: json-file
    options:
      max-size: "10m"
      max-file: "3"
```

- Docker 配置(推荐)

在1panel 中可以一键设置, 开启日志切割, 或者手动配置

```bash
vim /etc/docker/daemon.json
```
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

## Mysql部署注意事项

当时用 Mysql 镜像时, 如果不进行优化, 磁盘空间会被 binlog 占满

**⚠ 是否可以清理？**

取决于你是否有
- 主从复制？
- 数据备份依赖 binlog？
- 增量备份？
- point-in-time recovery？

如果 **没有主从 / 增量恢复需求**或者存在卷备份功能 → 可以安全清理旧日志。

- 显示 binlog 占用
```
SHOW BINARY LOGS;
```

- 立即清理, 保留最近 7 天
```
PURGE BINARY LOGS BEFORE NOW() - INTERVAL 7 DAY;
```

- 设置自动过期(7 天)
```
SET GLOBAL binlog_expire_logs_seconds = 604800;
```

推荐做法, 部署时, 指定 command

``` yaml
db:
  image: mysql:latest
  # 指定过期时间(3 天)
  command: --binlog-expire-logs-seconds=259200
  environment:
    MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
    MYSQL_DATABASE: ${MYSQL_DATABASE}
    MYSQL_USER: ${MYSQL_USER}
    MYSQL_PASSWORD: ${MYSQL_PASSWORD}
  volumes:
    - gitea_mysql:/var/lib/mysql
  restart: unless-stopped
  healthcheck:
    test: ["CMD-SHELL", "mysqladmin ping -h 127.0.0.1 -uroot -p$$MYSQL_ROOT_PASSWORD --silent"]
    interval: 10s
    timeout: 5s
    retries: 10
```

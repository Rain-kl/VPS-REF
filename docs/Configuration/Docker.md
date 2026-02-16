
## 访问鉴权中间件

基于 anubis , 增加密码鉴权方式和 IP 白名单验证模式

- AUTH_MODE: pow 为原版鉴权模式, 主要用于校验机器人爬虫的等, password 为密码鉴权, 主要用于给自部署的一些无鉴权应用在不修改源码的情况下加一层保护, 防止滥用
- VALID_MODE: jwt 为原版验证模式, 通过鉴权后系统会颁发 jwt token, 持有 jwt 的请求才可以访问应用. whitelist 为 IP 白名单模式, 通过鉴权的IP 会进入白名单, 持续请求会刷新白名单有效期, 默认 1 小时过期. 主要用于非浏览器应用例如 curl 请求的资源

```yaml
version: '3.8'

services:
  anubis:
    image: ghcr.io/rain-kl/anubis:latest
    ports:
      - "5000:8923"
#      - "9090:9090"
	# 如果anubis在反代程序后, 注释掉下面一行
#	command: ["-use-remote-address"]
    environment:
	  # 反代地址
      TARGET: "http://hubproxy:5000"
      AUTH_MODE: "password"
      VALID_MODE: "whitelist"
      # 访问密码, AUTH_MODE 需配置为password
      PASSWORD: "12345678"
      # 密码最大可重试次数
      PASSWORD_MAX_FAILS: "10"
      # 超过密码重试次数封禁时间
      PASSWORD_BAN_TIME: "900"
      # 白名单有效期
      WHITELIST_TIMEOUT: "3600"

  hubproxy:
    image: ghcr.io/sky22333/hubproxy
    container_name: hubproxy
    restart: always
```

## Cloudflared


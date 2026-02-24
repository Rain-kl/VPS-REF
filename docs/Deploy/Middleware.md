
## 访问鉴权中间件

**应用场景**: 不想被爬虫搜索引擎扫到, 不想被自动化脚本滥刷, 只想自己使用但是部署资源没有鉴权功能

基于 anubis , 增加密码鉴权方式和 IP 白名单验证模式

- AUTH_MODE: pow 为原版鉴权模式, 主要用于校验机器人爬虫的等, password 为密码鉴权, 主要用于给自部署的一些无鉴权应用在不修改源码的情况下加一层保护, 防止滥用
- VALID_MODE: jwt 为原版验证模式, 通过鉴权后系统会颁发 jwt token, 持有 jwt 的请求才可以访问应用. whitelist 为 IP 白名单模式, 通过鉴权的IP 会进入白名单, 持续请求会刷新白名单有效期, 默认 1 小时过期. 主要用于非浏览器应用例如 curl 请求的资源

完整文档查看 [Github](https://github.com/Rain-kl/anubis)

### 示例

- POW 基础模式
```
  anubis:
    image: ghcr.io/rain-kl/anubis:latest
    ports:
      - "59100:8923"
    environment:
      # 反代地址
      TARGET: "http://fast-note-sync-service:9000"
      AUTH_MODE: "pow"
      VALID_MODE: "jwt"
      DIFFICULTY: "4"
      DEFAULT_FALLBACK_ACTION: "ALLOW"
```

- Password 与 Whitelist 模式
```yaml
version: '3.8'

services:
  anubis:
    image: ghcr.io/rain-kl/anubis:latest
    ports:
      - "5000:8923"
	# 如果anubis在反代程序后, 注释掉下面一行
#	command: ["-use-remote-address"]
    environment:
	  # 反代地址
      TARGET: "http://hubproxy:5000"
      AUTH_MODE: "password"
      VALID_MODE: "whitelist"
      DIFFICULTY: "4"
      # 访问密码, AUTH_MODE 需配置为password
      PASSWORD: "12345678"
      # 密码最大可重试次数
      PASSWORD_MAX_FAILS: "10"
      # 超过密码重试次数封禁时间
      PASSWORD_BAN_TIME: "900"
      # 白名单有效期
      WHITELIST_TIMEOUT: "3600"
      # CHALLENGE/ALLOW, pow 模式如果设置为CHALLENGE,则搜索引擎无法检索
      DEFAULT_FALLBACK_ACTION: "CHALLENGE"
      
  hubproxy:
    image: ghcr.io/sky22333/hubproxy
    container_name: hubproxy
    restart: always
```

- 自定义规则
```
  anubis:
    image: ghcr.io/rain-kl/anubis:latest
    ports:
      - "59100:8923"
    volumes:  
	  - ./botPolicies.custom.yaml:/data/cfg/botPolicies.custom.yaml:ro
    environment:
      # 反代地址
      TARGET: "http://fast-note-sync-service:9000"
      AUTH_MODE: "pow"
      VALID_MODE: "jwt"
      DIFFICULTY: "4"
      DEFAULT_FALLBACK_ACTION: "ALLOW"
      POLICY_FNAME: "/data/cfg/botPolicies.custom.yaml"
```

```
# Custom policy for path-based allow/challenge rules.  
# Rule order matters: first matching ALLOW/CHALLENGE/DENY/WEIGHT returns immediately.  
  
bots:  
  # 1.1) Example allowlist rules for common static/public paths.  
  - name: allow-healthz  
    path_regex: ^/healthz$  
    action: ALLOW  
  
  # 2) Example challenge rule: always challenge sensitive paths.  
  - name: challenge-sensitive-paths  
    path_regex: ^/(admin|login|signin|wp-admin)(/.*)?$  
    action: CHALLENGE  
    challenge:  
      algorithm: fast  
      difficulty: 4  
  
  # Keep the built-in default rules after custom overrides.  
  # It Allows such as Googlebot, Cloudflare, and other well-behaved bots, and challenges based on user-agent, geoip, ASN, and system load.  
  - import: (data)/meta/default-config.yaml
```
## Cloudflared

**应用场景**: 域名访问某项服务, 但是不想配置 nginx. 不想开放端口, 服务器无公网 IP

```yaml
services:
  tunnel:
    container_name: cloudflared
    image: cloudflare/cloudflared:latest
    restart: unless-stopped
    command: tunnel run
    environment:
      - TUNNEL_TOKEN=YOUR_TOKEN
  web:
    image: nginx
```

**設定路由**：在 Cloudflare 後台將域名指向容器名稱（如上例的 `http://web:80`)

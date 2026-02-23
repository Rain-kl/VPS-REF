## 部署

```
CODER_ACCESS_URL=
CODER_HTTP_ADDRESS=0.0.0.0:7080
POSTGRES_DB=coder
POSTGRES_USER=coder
POSTGRES_PASSWORD=change-me
```

```
services:
  coder:
    image: ghcr.io/coder/coder:v2.30.1

    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    # getent group docker
    group_add:
      - "990"
    ports:
      - "27080:7080" 
    depends_on:
      db:
        condition: service_healthy
    environment:
      - CODER_ACCESS_URL
      - CODER_HTTP_ADDRESS
      - CODER_PG_CONNECTION_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@db/${POSTGRES_DB}?sslmode=disable

  db:
    image: postgres:17

    environment:
      - POSTGRES_PASSWORD
      - POSTGRES_USER
      - POSTGRES_DB
    healthcheck:
      test:
        [
          "CMD-SHELL",
          "pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}",
        ]
      interval: 5s
      timeout: 5s
      retries: 5
    volumes:
      - db_coder_data:/var/lib/postgresql/data

volumes:
  db_coder_data:

```

## 使用带桌面的工作空间

```
FROM codercom/example-desktop

WORKDIR /home/${USER}
```

```
# 在最后一行下增加以下内容

module "kasmvnc" {
  count               = data.coder_workspace.me.start_count
  source              = "registry.coder.com/coder/kasmvnc/coder"
  version             = "1.2.7"

  agent_id            = coder_agent.main.id
  desktop_environment = "xfce"
  subdomain           = false
}

```


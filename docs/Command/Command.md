## 注册系统服务

1. **创建服务文件**： 创建或编辑 systemd 服务文件，使其使用新的虚拟环境：

```
sudo nano /etc/systemd/system/example.service
```

服务文件内容如下：

```bash
[Unit]
Description=My App Service
After=network.target

[Service]
Type=simple
ExecStart=/opt/myapp/myapp
WorkingDirectory=/opt/myapp
Restart=always
RestartSec=5
User=www-data
Environment=ENV=production

[Install]
WantedBy=multi-user.target
```

| **字段**      | **含义**                     |
| ----------- | -------------------------- |
| Description | 服务描述                       |
| After       | 在网络就绪后启动                   |
| ExecStart   | 启动命令（必须是**绝对路径**）          |
| Restart     | 崩溃自动重启                     |
| User        | 以哪个用户运行（**不要用 root，除非必须**） |
| WantedBy    | 指定运行级别（开机启动）               |

1. **开机自启**：

```
sudo systemctl daemon-reload
sudo systemctl enable example
sudo systemctl start example
```

2. **检查服务状态与日志**：

```
sudo systemctl status example

sudo journalctl -u example -f
```

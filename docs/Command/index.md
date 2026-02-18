
### 核心工具

```
apt install -y sudo curl wget vim iperf3 vnstat screen btop ncdu duf
```

- **iperf3**: 测试网络带宽
```bash
# 服务器端
iperf3 -s
# 客户端
iperf3 -c 服务器IP
```

- **vnstat**: 统计网卡累计流量
```bash
vnstat
vnstat -d   # 每日统计
vnstat -l   # 实时流量
```

- **screen**: 创建可后台运行的终端会话
```bash
# 分离
Ctrl + A + D
# 创建
screen -S <name>
# 进入
screen -r
```

- **ncdu** : 快速查看哪个目录占用最多空间。

- **duf**: 更好看的 df（查看磁盘使用情况）。

### 运维工具

```
apt install -y fd-find lsd
```

- **ctop** 查看 docker 容器信息
```
sudo apt-get install ca-certificates gnupg lsb-release
curl -fsSL https://azlux.fr/repo.gpg.key | sudo gpg --dearmor -o /usr/share/keyrings/azlux-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/azlux-archive-keyring.gpg] http://packages.azlux.fr/debian \
  $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/azlux.list >/dev/null
sudo apt-get update
sudo apt-get install docker-ctop
```

- **fd-find**: 更快、更好用的 find 替代品。
```bash
# 搜索文件名
fd keyword

# 指定目录搜索
fd keyword /var/log

# 按扩展名搜索
fd -e log

# 搜索目录
fd -t d nginx
```

### 日用工具

```
apt install -y ripgrep
```

```bash
# dust
brew install dust
# yazi
brew install yazi ffmpeg sevenzip jq poppler fd ripgrep fzf zoxide resvg imagemagick font-symbols-only-nerd-font

```

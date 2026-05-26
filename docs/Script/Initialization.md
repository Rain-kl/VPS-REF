
## 换源工具

- GNU/Linux 更换系统软件源

```bash
bash <(curl -sSL https://linuxmirrors.cn/main.sh)
```

- Docker 安装与换源

```bash
bash <(curl -sSL https://linuxmirrors.cn/docker.sh)
```

- Docker 更换镜像加速器

```bash
bash <(curl -sSL https://linuxmirrors.cn/docker.sh) --only-registry
```

## 安装1panel

```
bash -c "$(curl -sSL https://resource.fit2cloud.com/1panel/package/v2/quick_start.sh)"
```

## 系统优化

- 时区设置

```
timedatectl set-timezone Asia/Shanghai
```

- 内存优化

```
curl -fsSL https://raw.githubusercontent.com/Rain-kl/tunescipt/main/ram-tune.sh -o /tmp/ram-tune.sh && sudo bash /tmp/ram-tune.sh
```

## 脚本工具箱

- KEJILION.SH - 科技lion一键脚本工具

```
bash <(curl -sL kejilion.sh)
```

## 网络优化

### BBR 调优

```jsx
wget http://sh.xdmb.xyz/tcp.sh && bash tcp.sh
```

>*ICMP不可达喵*: 
>
>一般无脑选11，BBR+FQ即可，有时候可以用13的BBR+CAKE，还有人说PIE好用，其实一般11就行了，其他容易越调越垃圾。
>
> 每个服务器都不同，这个脚本可用可不用，我一般默认开11的


## 安全设置

- 修改 ssh 端口

修改为11022 端口

```
sudo sed -i 's/^#\?Port 22.*/Port 11022/g' /etc/ssh/sshd_config
```

- 配置fail2ban

```
apt install -y fail2ban
vim /etc/fail2ban/jail.local
```

```
[DEFAULT]
ignoreip = 127.0.0.1/8 ::1
allowipv6 = auto
backend = systemd
banaction = nftables-multiport

[sshd]
enabled = true
filter = sshd
port = ssh
bantime = 86400
findtime = 86400
maxretry = 3
```

```
sudo systemctl enable fail2ban
sudo systemctl restart fail2ban
fail2ban-client status
```


## 大杂烩
### [DDNS脚本](https://github.com/kkkgo/UE-DDNS)

```
curl -skLo ue-ddns.sh ddns.03k.org  
sh ue-ddns.sh
然后根据提示完成操作
最后设置定时任务
sudo apt-get update
sudo apt-get install cron
crontab -e
*/10 * * * * /root/(你脚本的名字)@cloudflare_IPV4_URL.sh
```


## 参考

- [【配置优化】我拿到VPS服务器必做的那些事](https://linux.do/t/topic/160305)
- [常见脚本推荐](https://meowvps.com/blog/script/)
- [LinuxMirrors](https://github.com/SuperManito/LinuxMirrors)
	
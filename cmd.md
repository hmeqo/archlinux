### 查看本目录下非当前用户和组的文件

```bash
fd -u --owner "!$USER" --owner ":!$USER"
```

### 生成自签名证书

- openssl

  ```bash
  openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 \
    -nodes -keyout key.pem -out cert.pem \
    -addext "subjectAltName=IP:127.0.0.1,DNS:localhost"
  ```

- sscg

  ```bash
  sscg
  ```

### 创建系统用户

```bash
sudo useradd \
  --system \
  --home /var/lib/xxx \
  --create-home \
  --shell /usr/bin/nologin \
  xxx
```

### 抓包

```bash
## DNS 流量
sudo tcpdump -ni any port 53 or port 853

## DNSoverTLS 流量
sudo tshark -i any -Y "tcp.port==853"
```

### 在两台机器同步系统

```bash
rsync -aAXH \
  --numeric-ids \
  --info=progress2 \
  --delete \
  --exclude={"/dev/*","/proc/*","/sys/*","/run/*","/tmp/*","/mnt/*","/media/*","/lost+found"} \
  root@source:/ \
  target/
```

### 重命名用户并迁移家目录

> 以 root 执行。操作前确认旧用户已注销, `loginctl list-sessions` 查看当前用户会话,  
> 如有用户进程, `systemctl stop user@<UID>.service` 停止对应用户服务

```bash
old=olduser            # 旧用户名
new=newuser            # 新用户名
old_home=/home/$old
new_home=/home/$new

# 重命名用户、迁移家目录、重命名同名用户组
usermod -l "$new" -d "$new_home" -m "$old"
groupmod -n "$new" "$old"

# usermod -l “Nothing else is changed”：subordinate 条目仍留在旧用户名下，添加后删除旧键
usermod --add-subuids 100000-165535 --add-subgids 100000-165535 "$new"
sed -i "/^$old:/d" /etc/subuid /etc/subgid

# 修复家目录内指向旧路径的符号链接
find "$new_home" -type l -lname "*$old*" -exec sh -c '
  old=$1 new=$2
  shift 2
  for f; do
    ln -sfnv "$(readlink "$f" | sed "s|$old|$new|g")" "$f"
  done
' _ "$old_home" "$new_home" {} +

# 替换家目录内文本文件中的旧路径（跳过 .git，不跟随符号链接）
rg -lF0 --hidden --no-ignore -g '!.git' -e "$old_home" "$new_home" \
  | xargs -0 -r sed -i "s|$old_home|$new_home|g"
```

- KDE 用户需在迁移后删除 `~/.config/session/dolphin_dolphin_dolphin`, 否则 dolphin 无法打开

- 家目录之外 (`/etc`、`/var` 下的 unit、cron、配置) 可能仍有旧路径, 需另行检查

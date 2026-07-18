### 查看本目录下非当前用户和组的文件

```bash
fd -u --owner "!$USER" --owner ":!$USER"
```

### 全局搜索替换

```bash
rg -l '<pattern>' </path/to> | sed 's/.*/"&"/' | xargs sed -i 's/<pattern>/<replacement>/g'
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

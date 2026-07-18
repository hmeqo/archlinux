# Flox

基于 Nix 的环境管理工具，用 `manifest.toml` + CLI 代替手写 Nix expression。需先安装 Nix（见 [nix.md](./nix.md)）。

---

## 安装

```bash
# 1. /etc/nix/nix.conf 追加 flox 缓存（避免本地构建）
extra-trusted-substituters = https://cache.flox.dev
extra-trusted-public-keys = flox-cache-public-1:7F4OyH7ZCnFhcze3fJdfyXYLQw/aV7GEed86nQ7IsOs=
```

```bash
# 2. 重启 daemon
sudo systemctl restart nix-daemon.service
```

```bash
# 3. 安装 flox
nix profile install --accept-flake-config 'github:flox/flox/latest'
flox --version
```

## 日常命令

```bash
flox init --name myproject     # 创建环境，生成 .flox/
flox search qt5                 # 搜索包
flox show qt515.qtbase          # 包详情
flox install nodejs_20 cmake    # 安装依赖
flox activate                   # 进入环境
```

环境描述在 `.flox/env/manifest.toml`：

```toml
version = 1

[install]
nodejs_20.pkg-path = "nodejs_20"
cmake.pkg-path = "cmake"

[vars]
NODE_ENV = "development"
```

## 实用场景

```bash
# Qt5 开发
flox init --name qt5-app
flox install qt515.qtbase qt515.qttools cmake gcc13
flox activate

# CUDA 11.8
flox init --name cuda118
flox install cudaPackages_11_8.cudatoolkit cudaPackages_11_8.cudnn python311
flox activate

# Node / Python 固定版本
flox install nodejs_18
flox install nodejs_20
flox install python311
flox install python312
```

---

参考：[Flox 文档](https://flox.dev/docs)

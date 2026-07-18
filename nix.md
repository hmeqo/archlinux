# Nix on Arch Linux

---

## 安装

```bash
sudo pacman -S nix
sudo systemctl enable --now nix-daemon.service
nix --version    # 需 ≥ 2.18.0
```

## 配置

### `/etc/nix/nix.conf`

```ini
# 构建隔离用户组（pacman 安装时自动创建）
build-users-group = nixbld

# 数据存到 XDG 目录而不是 ~/.nix-profile
use-xdg-base-directories = true

# 启用新版 CLI 和 flakes
extra-experimental-features = nix-command flakes
```

改完后重启 daemon：

```bash
sudo systemctl restart nix-daemon
```

### 允许 unfree 包

- 方式一：系统级配置

  ```ini
  # /etc/nix/nix.conf 添加
  nixpkgs-config = /etc/nix/nixpkgs-config.nix
  ```

  ```nix
  # /etc/nix/nixpkgs-config.nix
  { allowUnfree = true; }
  ```

- 方式二：用户级配置

  ```nix
  # ~/.config/nixpkgs/config.nix
  { allowUnfree = true; }
  ```

- 方式三：环境变量

  ```bash
  export NIXPKGS_ALLOW_UNFREE=1
  ```

> `nix shell`、`nix develop` 默认 pure evaluation 不读外部状态，需 `--impure`：  
> `nix shell nixpkgs#google-chrome --impure`

参考：[NixOS Wiki - Unfree Software](https://nixos.wiki/wiki/Unfree_Software)

## 日常命令

```bash
# 搜索包（也支持正则）
nix search nixpkgs python311

# 在线搜索（更方便看版本）
# https://search.nixos.org/packages

# 临时环境，退出即消失
nix shell nixpkgs#python311 nixpkgs#nodejs_18

# 直接运行
nix run nixpkgs#hello

# 安装到 profile（持久）
nix profile install nixpkgs#hello
nix profile list
nix profile remove hello
```

## 进阶

### nix.conf 调优

```ini
# 用满所有 CPU 核心
max-jobs = auto
```

### 更改缓存镜像源

Nix 默认從 `cache.nixos.org` 下载预编译包（substituter）。国内可换成镜像站加速：

```ini
# /etc/nix/nix.conf 添加
extra-substituters = https://mirrors.tuna.tsinghua.edu.cn/nix-channels/store
```

### 声明式配置

Nix 的核心优势是声明式——用配置文件描述期望状态，而不是手动执行命令。

**home-manager** — 声明式管理用户级包和 dotfiles：

```nix
# ~/.config/home-manager/home.nix
{ pkgs, ... }: {
  home.packages = with pkgs; [
    neovim
    git
    gh
    nodejs_20
  ];

  programs.bash.enable = true;

  home.stateVersion = "23.11";
}
```

```bash
# 安装 home-manager
nix profile install nixpkgs#home-manager
home-manager switch    # 应用配置
```

**flake.nix** — 项目级声明式开发环境：

```nix
{
  description = "声明式 dev shell";
  inputs.nixpkgs.url = "github:NixOS/nixpkgs/nixpkgs-23.11";

  outputs = { self, nixpkgs }:
    let pkgs = nixpkgs.legacyPackages.x86_64-linux;
    in {
      devShells.x86_64-linux.default = pkgs.mkShell {
        buildInputs = with pkgs; [
          cmake
          gcc13
        ];
      };
    };
}
```

每次改完 `flake.nix` 后执行 `nix develop` 进入环境，一切自动对齐。

## 常见问题

```bash
# locale 警告
export LOCALE_ARCHIVE=/usr/lib/locale/locale-archive

# sandbox 构建失败 → 关掉
# /etc/nix/nix.conf 加：sandbox = false
sudo systemctl restart nix-daemon

# 清理无用 store
nix store gc

# 手动去重硬链接（auto-optimise-store = true 每次操作后扫全量，store 越大越慢，不推荐）
nix store optimise
```

---

Flox 环境管理见 [flox.md](./flox.md)。

参考：[ArchWiki Nix](https://wiki.archlinux.org/title/Nix)

## Wiki

Archwiki: <https://wiki.archlinux.org/title/Main_page>

- [Wiki](#wiki)
  - [GNU/Linux 基础目录结构](#gnulinux-基础目录结构)
  - [WINE/PROTON 运行 Windows 应用/游戏](#wineproton-运行-windows-应用游戏)
    - [WINE 生态中的各种工具介绍](#wine-生态中的各种工具介绍)
    - [WINE/PROTON GUI 启动器](#wineproton-gui-启动器)
  - [DKMS](#dkms)

### GNU/Linux 基础目录结构

| 位置                  | 描述                                                                                                                                                  |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/boot`               | 存放启动镜像各种启动配置的文件夹                                                                                                                      |
| `/dev`                | 设备目录, 例如和主板连接的硬盘, u盘, 鼠标, 键盘等设备都在这里显示                                                                                     |
| `/etc`                | 配置文件目录                                                                                                                                          |
| `/home`               | 用户目录, 例如用户 `<user>` 的用户目录是 `/home/<user>`, 这个位置并非绝对的, 可以修改                                                                 |
| `/mnt`                | 用于挂载其他分区, 例如将 windows 盘挂载到 `/mnt/windows`                                                                                              |
| `/opt`                | 用于存放软件包的目录, 通常是一些因某些原因不能把软件拆分开的商业软件                                                                                  |
| `/proc`               | 进程目录, 在这里可以看到当前正在运行的进程, 以及各种信息                                                                                              |
| `/root`               | root 用户的家目录                                                                                                                                     |
| `/run`                | 用于存放运行时数据, 套接字, 和其他类似文件, 通常仅对特权程序可写                                                                                      |
| `/srv`                | srv - Service Data, 用于存放服务的目录                                                                                                                |
| `/sys`                | 一个虚拟内核文件系统, 主要提供与内核接口相关的 API<br>例如临时关闭 Intel CPU 睿频: `echo 1 \| sudo tee /sys/devices/system/cpu/intel_pstate/no_turbo` |
| `/tmp`                | 临时文件目录                                                                                                                                          |
| `/usr`                | 用于存放应用程序的目录                                                                                                                                |
| `/var`                | 用于存放数据的目录 (随时会变化的数据)                                                                                                                 |
| `/bin` → `/usr/bin`   | 符号链接                                                                                                                                              |
| `/lib` → `/usr/lib`   | 符号链接                                                                                                                                              |
| `/lib64` → `/usr/lib` | 符号链接                                                                                                                                              |
| `/sbin` → `/usr/bin`  | 符号链接                                                                                                                                              |
| `EFI`                 | EFI 分区目录, 如果你把 EFI 分区挂载到了 `/boot`, 那么你可以在 `/boot/EFI` 中找到启动项<br>如果你的 EFI 挂载点是 `/`, 那么对应的 EFI 目录则是 `/EFI`   |

用户级

| 位置         | 系统对应   | 描述                                  |
| ------------ | ---------- | ------------------------------------- |
| .cache       | /var/cache | 缓存                                  |
| .config      | /etc       | 配置                                  |
| .local/bin   | /usr/bin   | 可执行文件                            |
| .local/share | /usr/share | 数据                                  |
| .local/state | /var/lib   | 状态数据, 包括 log, 通常在 <app>/logs |
| .local/lib   | /usr/lib   | 库文件                                |
| .local/opt   | /opt       | 软件安装                              |

### WINE/PROTON 运行 Windows 应用/游戏

#### WINE 生态中的各种工具介绍

- Wine

  - 是什么: Wine 是一个开源兼容层, 允许在类 Unix 操作系统 (如 Linux、macOS) 上运行设计为在 Microsoft Windows 上运行的应用程序 (特别是那些使用 Win32 API 的应用程序)  
    其原理是重写了 Windows 所有的 dll, 将对 Windows 的 API 调用转换为 Linux 的 API 调用

- DXVK

  DXVK 一个用于转换 DirectX9/10/11 API 为 Vulkan API 的兼容层, 能大幅度提升游戏性能, 甚至超越 Windows, DXVK 不止能用于 Linux, 也能在 Windows 上使用

- VKD3D

  VKD3D 一个用于转换 DirectX12 API 为 Vulkan API 的兼容层, 不过 VKD3D 不像 DXVK 那样已经发展了很久, VKD3D 的游戏性能在某些情况下可能只有 Windows 的 2/3

- Proton

  - 是什么: Proton 是 Valve 为其数字发行平台 Steam 开发的一个开源工具, 基于 Wine, 但专门为运行 Windows 游戏进行了优化  
    Proton 整合了 DXVK, VKD3D 等一系列工具, Valve 对 Proton 的更改都会回馈到上游 (Wine, DXVK, VKD3D 等), Proton 极大促进了 Linux 游戏/软件生态的发展
  - 与 Wine 的关系: Proton 构建于 Wine 之上, 使用 Wine 的代码库作为其核心, 并添加了 Valve 自己的补丁、优化和额外功能, 以提高游戏的兼容性和性能

- Proton-GE

  由 GloriousEggroll 创建的一个 Proton 定制版, 相比于 Proton, 整合了更多 Proton 没有或暂时没有的功能, 有更好的游戏兼容性

- Wine-GE (自 Wine-9.0 起不再维护)

  由 GloriousEggroll 创建的一个 Wine 定制版, Wine-GE 是为非 Steam 游戏分发的版本, 因为 Proton 是专为 Steam 开发, 有很多非 Steam 游戏不需要的功能

- UMU-Launcher

  由于 Proton 是专门为 Steam 游戏开发的, 在 Wine9.0 之前, 一般建议用 Wine-GE 运行非 Steam 游戏/应用, Proton-GE 运行 Steam 游戏  
  但在 9.0 之后, GE 作者不再分发 Wine-GE 版本, 转而开发了 UMU-Launcher, 用 Proton-GE 运行非 Steam 游戏/应用

- GPTK (Game Porting Toolkit)

  Apple 为 macOS 开发的基于 Wine 的游戏兼容层

> [!NOTE]
> Proton 能够以高性能运行 Windows 游戏, 主要功劳在于 DXVK 和 VKD3D  
> 各种利用 Wine 运行 Windows 游戏的启动器都默认帮你配好了 DXVK, VKD3D 等工具, 其游戏性能和 Proton 不会有太大区别

#### WINE/PROTON GUI 启动器

- Steam

  伟大无需多言

- lutris

  流行且通用的游戏/应用启动/管理工具

- bottles

  通用 win 应用/游戏启动/管理工具

- faugus-launcher

  利用 UMU-Launcher 的游戏/应用启动器

- heroic-games-launcher

  非官方 Epic 游戏启动器

- CrossOver

  付费版 Wine

### DKMS

> 动态内核模块支持 （Dynamic Kernel Module Support，DKMS）是用来生成Linux的内核模块的一个框架，其源代码一般不在Linux内核源代码树。 当新的内核安装时，DKMS支持的内核设备驱动程序 到时会自动重建。 DKMS可以用在两个方向：如果一个新的内核版本安装，自动编译所有的模块，或安装新的模块（驱动程序）在现有的系统版本上，而不需要任何的手动编译或预编译软件包需要。例如，这使得新的显卡可以使用在旧的Linux系统上。

简单说，Linux 内核由于 ABI 不兼容，编译好的内核模块通常只能由特定版本的内核加载。
想要增加额外的内核模块（如显卡驱动），要么每当内核更新时手动重新编译，要么使用 DKMS。
DKMS 本质上是一个自动化管理系统，它利用内核头文件，在内核升级或模块安装时，通过触发器自动为系统中已安装的内核重新编译并部署对应的模块，省去了人工手动维护的麻烦。

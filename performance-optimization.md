# Performance Optimization

ArchWiki指路: <https://wiki.archlinuxcn.org/wiki/%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96>

需要注意: 优化选项之间或与其他程序可能不能兼容

## CPU

### scx-scheds

安装

```bash
sudo pacman -S scx-scheds scx-tools
```

编辑配置文件 `/etc/scx_loader.toml`

```toml
default_sched = "scx_lavd"
default_mode = "LowLatency" # Auto, Gaming, Performance, LowLatency, PowerSave
```

最后启动服务即可 `sudo systemctl enable --now scx_loader.service`

可用的调度器: <https://wiki.cachyos.org/configuration/sched-ext/>

cli

- 当前调度器和模式

  ```bash
  scxctl get
  ```

- 设置调度器和模式

  `sudo scxctl switch -s <调度器> -m <模式>`

  所有可用模式 (具体看调度器支持):  auto, gaming, performance, lowlatency, powersave

  示例

  ```bash
  sudo scxctl switch -s lavd -m lowlatency
  ```

### Ananicy

> Ananicy是一个用于自动调节可执行程序nice值的守护进程。nice值表示了在为特定可执行程序分配CPU资源时的优先级。

- 安装

  想激进一点可以装 cachyos-ananicy-rules

  ```bash
  sudo pacman -S ananicy-cpp ananicy-rules-git
  ```

- 启用服务

  ```bash
  sudo systemctl enable --now ananicy-cpp
  ```

> [!WARNING]
> ananicy-cpp 和 gamemode 都会修改游戏进程的 nice, 如果要一起用, 最好只让 ananicy-cpp 或 gamemode 其中一个管理游戏进程的 nice

### preempt=full

PS: 6.12 开始已是默认

关于是否启用 preempt=full 的讨论: <https://www.reddit.com/r/Fedora/comments/158fy6x/comment/l5kwhvv/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button>

在内核参数添加 `preempt=full` 然后重新启动即可启用

判断当前实时内核状态, 可通过

```bash
$ sudo cat /sys/kernel/debug/sched/preempt
none voluntary (full) lazy 
```

## 显卡

### envycontrol

使用 envycontrol 优化显卡/双显卡

```bash
# 针对双显卡
sudo envycontrol -s hybrid --rtd3 3
# NVIDIA
sudo envycontrol -s nvidia
```

### nvidia-powerd

让 nvidia 自动调整功耗上限

```bash
sudo systemctl enable --now nvidia-powerd.service
```

`nvidia-smi -q | grep -i "Power Limit" -A4` 查看功耗情况

## 内存

### 优化内存透明大页

写入以下内容到 `/etc/tmpfiles.d/thp-tuning.conf`:

```conf
w /sys/kernel/mm/transparent_hugepage/defrag - - - - defer+madvise
w /sys/kernel/mm/transparent_hugepage/khugepaged/max_ptes_none - - - - 409
```

重启或运行下方命令生效

```bash
sudo systemd-tmpfiles --create /etc/tmpfiles.d/thp-tuning.conf
```

确认生效

```bash
cat /sys/kernel/mm/transparent_hugepage/defrag
cat /sys/kernel/mm/transparent_hugepage/khugepaged/max_ptes_none

# always defer [defer+madvise] madvise never  # [] 内为生效选项
# 409
```

## 启动优化

### mkinitcpio的systemd钩子

备注: 新的 arch 系统默认应该是 systemd 了

systemd 钩子可异步加载模块, 开机速度相对 udev 快一些, 可能不支持老旧硬件

mkinitcpio 默认的钩子组合是以udev为主的, 如果需要更换为systemd,
编辑 `/etc/mkinitcpio.conf`, 找到 HOOKS 配置项, 并替换为以下内容

PS: HOOKS上方应该是有注释说明 systemd 配置的示例

```conf
HOOKS=(base systemd autodetect microcode modconf kms keyboard sd-vconsole sd-encrypt block filesystems fsck)
```

## Tweaks

### fstrim

它的主要作用是通知固态硬盘哪些数据块已经被系统删除或废弃，从而允许 SSD 提前清理这些区块以保持读写性能并延长硬件寿命

```bash
sudo systemctl enable --now fstrim.timer
```

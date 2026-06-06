## 常用

### 修改分区 Label

- ext4

  ```bash
  sudo e2label <device> <newlabel>
  ```

- btrfs

  ```bash
  # 对于已挂载的设备
  sudo btrfs filesystem label <mountpoint> <newlabel>
  # 否则
  sudo btrfs filesystem label <device> <newlabel>
  ```

- fat32

  ```bash
  sudo fatlabel <device> <newlabel>
  ```

- ntfs

  ```bash
  sudo ntfslabel <device> <newlabel>
  ```

## btrfs

- 使用情况

  ```bash
  btrfs filesystem usage <mountpoint>
  ```

- 占用情况

  ```bash
  btrfs filesystem df <mountpoint>
  ```

- 整理碎片

  ```bash
  btrfs filesystem defragment -rv <mountpoint>
  ```

- 转换用户数据为 dup

  ```bash
  btrfs balance start -dconvert=dup <mountpoint>
  ```

- 优化 hdd

  启用 zstd:5 压缩, 启用元数据的dup，如果有双盘可以组raid1，单盘可以用dup (下策)

## SSD

### SSD 优化

```bash
sudo systemctl enable --now fstrim.timer
```

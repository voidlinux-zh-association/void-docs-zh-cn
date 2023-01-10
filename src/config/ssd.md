# 固态硬盘

安装后，您需要为固态驱动器启用 TRIM。 你可以通过运行检查哪些设备允许 TRIM： 

```
$ lsblk --discard
```

如果 DISC-GRAN（丢弃颗粒度）和 DISC-MAX（丢弃最大字节数）列非零，这意味着该块设备有 TRIM 支持。如果你的固态硬盘分区没有显示支持 TRIM，请确认你选择了一个支持 TRIM 的文件系统（ext4, Btrfs, F2FS, 等等）。注意，F2FS 需要内核 4.19 或更高版本来支持 TRIM。

要一次性运行 TRIM，你可以手动运行 [`fstrim(8)`](https://man.voidlinux.org/fstrim.8) 。例如，如果你的 `/` 目录在一个 SSD 上。

```
# fstrim /
```

要自动运行 TRIM，使用 cron 或在 `/etc/fstab` 中添加 `discard` 选项。

## 使用cron的定期TRIM

将以下行添加到 `/etc/cron.weekly/fstrim`:

```
#!/bin/sh

fstrim /
```

最后，使该脚本可执行：

```
# chmod u+x /etc/cron.weekly/fstrim
```

## 使用 fastab discard 和 持续 trim

你可以使用连续或定期的 TRIM，但如果你的 SSD 不能正确处理 NCQ，则不建议使用连续 TRIM。请参考内核的[黑名单](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/drivers/ata/libata-core.c?h=v5.8&id=bcf876870b95592b52519ed4aafcf9d95999bc9c#n3774)。

编辑　`/etc/fstab` ，添加 `discard` 选项以阻止需要TRIM的设备。

例如，如果 `/dev/sda1` 是一个 SSD 分区，格式化为 ext4，挂载 在 `/`: 

```
/dev/sda1  /           ext4  defaults,discard   0  1
```

## LVM

要为 LVM 的命令（`lvremove`, `lvreduce`, 等等）启用 TRIM，编辑 `/etc/lvm/lvm.conf` ，取消对 `issue_discards` 选项的注释，并将其设置为`1`。

```
issue_discards=1
```

## LUKS

**警告**: 在为你的 LUKS 分区启用 discard 之前，请注意[安全问题](https://wiki.archlinux.org/index.php/Dm-crypt/Specialties#Discard/TRIM_support_for_solid_state_drives_(SSD))。

要打开一个加密的 LUKS 设备并允许 discards ，用 `--allow-discards` 选项打开设备:

```
# cryptsetup luksOpen --allow-discards /dev/sdaX luks
```

### 非 root 设备

编辑 `/etc/crypttab`，为 SSD 上的设备设置 `discard` 选项。例如，如果你有一个 LUKS 设备，名称为 `externaldrive1`，设备为 `/dev/sdb2` ，密码为 `none` 。

```
externaldrive1  /dev/sdb2   none    luks,discard
```

### Root 设备

如果你的根设备在 LUKS 上，把 `rd.luks.allow-discards` 添加到 `CMDLINE_LINUX_DEFAULT`。在 GRUB 的情况下，编辑 `/etc/default/grub` ：

```
GRUB_CMDLINE_LINUX_DEFAULT="rd.luks.allow-discards"
```

### 验证配置

要验证你是否已为 LUKS 正确配置 TRIM，请运行： 

```
# dmsetup table /dev/mapper/crypt_dev --showkeys
```

如果此命令输出包含字符串 `allow_discards`， 在你的 LUKS 设备上成功启用了 TRIM。 

## ZFS

在 ZFS 池上运行 `trim` 之前，确保池中的所有设备都支持它：

```
# zpool get all | grep trim
```

如果池允许 `aurotrim` （默认设置为 `off`），你可以定期或者自动 `trim` zfs池。要一次性 `trim` `你的 zfs 池的名字`：

```
# zpool trim yourpoolname
```

### 定期 TRIM

将以下行添加到 `/etc/cron.daily/ztrim`:

```
#!/bin/sh
zpool trim yourpoolname
```

最后，使该脚本可执行：

```
# chmod u+x /etc/cron.daily/ztrim
```

### 自动 trim

设置 autotrim  `yourpoolname`, 请运行:

```
# zpool set autotrim=on yourpoolname
```

# 全盘 ZFS

因为 Void 的安装程序不支持 ZFS，所以有必要通过 chroot 来安装。除了一些关于引导程序和 initramfs 支持的注意事项外，在 ZFS 根文件系统上安装 Void 与其他高级安装没有明显区别。[ZFSBootMenu](https://zfsbootmenu.org) 是一个从头开始设计的引导程序，支持直接从 ZFS 池中启动 Linux 发行版。然而，也可以使用传统的引导程序与ZFS根文件系统。

## ZFSBootMenu

尽管它可以启动（并且可以在上面运行）各种各样的发行版，但 ZFSBootMenu 官方认为 Void 是一等的发行版。ZFSBootMenu 支持原生的 ZFS 加密，提供方便的恢复环境，可用于克隆先前的快照或在预启动环境中执行高级操作，并支持从任何可被现代 ZFS 驱动程序导入的池中启动。ZFSBootMenu 文档除其他内容外，还提供了几个从头开始安装 Void 系统的[步骤指南](https://docs.zfsbootmenu.org/en/latest/guides/void-linux.html)。[UEFI 指南](https://docs.zfsbootmenu.org/en/latest/guides/void-linux/uefi.html)描述了现代系统引导 Void 系统的过程。对于传统的 BIOS 系统，[syslinux 指南](https://docs.zfsbootmenu.org/en/latest/guides/void-linux/syslinux-mbr.html)提供了类似的说明。


## 传统引导程序

对于那些希望放弃 ZFSBootMenu 的人来说，可以用另一个引导程序来引导 Void 系统。为了避免不必要的复杂性，使用ZFSBootMenu 以外的引导程序的系统应该计划使用单独的 `/boot`，它位于ext4或xfs文件系统中。

### 安装媒介

把 Void 安装到 ZFS 根目录下需要一个带有 ZFS 驱动的安装介质。可以从官方构建自定义镜像 [void-mklive](https://github.com/void-linux/void-mklive) 提供命令行选项 `-p zfs` 到 `mklive.sh` 脚本。对于 `x86_64` 系统来说，获取一个预先构建的 [hrmpf](https://github.com/leahneukirchen/hrmpf/releases) 镜像可能更方便。这些镜像由 Void 团队成员维护，是标准 Void Live 镜像的扩展，包括预编译的ZFS模块和其他有用的工具。

### 磁盘分区

在启动一个支持ZFS的实时镜像后，对你的[磁盘进行分区](../live-images/partitions.md)。分区指南中的注意事项也适用于ZFS安装，除了

- 引导分区应该被认为是必要的，除非你打算使用 `gummiboot`，它期望您的 EFI 系统分区将安装在 `/boot`. （这里不讨论这种替代配置。）
   
- 除了任何 EFI 系统分区、GRUB BIOS 启动分区、swap 分区或启动分区之外，磁盘的其余部分通常应该是一个类型代码为BF00 的单一分区，该分区将专门用于一个 ZFS 池。在单个磁盘上创建单独的 ZFS 池没有任何好处。

根据需要，使用格式化 EFI 系统分区 [mkfs.vfat(8)](https://man.voidlinux.org/mkfs.vfat.8) 和引导分区 使用 [mke2fs(8)](https://man.voidlinux.org/mke2fs.8) 或 [mkfs.xfs(8)](https://man.voidlinux.org/mkfs.xfs.8) 。 初始化任何 swap 空间 使用 [mkswap(8)](https://man.voidlinux.org) 。 

> 可以将 Linux 交换空间放在 ZFS zvol 上,尽管在高内存压力下可能会有内核锁死的风险。 本指南 在 zvol 上的交换空间问题上不采取任何立场。 然而，如果你想使用悬浮到磁盘（休眠），注意内核不能从存储在zvol上的内存镜像中恢复。 你需要一个专门的交换分区来使用休眠。除了这个注意事项之外，在使用ZFS根目录时，恢复一个暂停的映像不需要特别的考虑。

### 创建 ZFS 池 

使用 [zpool(8)](https://man.voidlinux.org/zpool.8) 在为其创建的分区上创建一个 ZFS 池 `/dev/disk/by-id/wwn-0x5000c500deadbeef-part3`:

```
# zpool create -f -o ashift=12 \
    -O compression=lz4 \
    -O acltype=posixacl \
    -O xattr=sa \
    -O relatime=on \
    -o autotrim=on \
    -m none zroot /dev/disk/by-id/wwn-0x5000c500deadbeef-part3
```

根据需要调整池（`-o`）和文件系统（`-O`）选项，并将分区标识符 `wwn-0x5000c500deadbeef-part3` 替换为要使用的实际分区的标识符。

> 当添加磁盘或分区到 ZFS 池时，一般来说，最好通过在 `/dev/disk/by-id` 或（在UEFI系统上）`/dev/disk/by-partuuid` 中创建的符号链接来引用它们。这样，即使磁盘命名在某个时候发生变化，ZFS也会识别正确的分区。使用传统的设备节点如 `/dev/sda3` 可能会导致间歇性的导入失败。

接下来，用一个临时的、备用的根路径导出并重新导入池子:

```
# zpool export zroot
# zpool import -N -R /mnt zroot
```

### 创建初始文件系统

ZFS 池上的文件系统布局是灵活的。 但是，通常将操作系统根文件系统（“引导环境”）置于 ROOT 父级下：

```
# zfs create -o mountpoint=none zroot/ROOT
# zfs create -o mountpoint=/ -o canmount=noauto zroot/ROOT/void
```

在 `mountpoint=/` 的文件系统上设置 `canmount=noauto` 是非常有用的，因为它允许创建多个启动环境（这些环境可能是普通 Void 安装的克隆，也可能包含完全独立的发行版），而不用担心ZFS自动挂载会试图挂载一个而不是另一个。

为了将用户数据与操作系统分开，创建一个文件系统来存储主目录:

```
# zfs create -o mountpoint=/home zroot/home
```

可以根据需要创建其他文件系统

### 挂载 ZFS 层次结构


所有的 ZFS 文件系统都应该安装在先前重新导入时建立的 `/mnt` 备用根目录下。在允许 ZFS 自动挂载其他所有的文件系统之前，先挂载纯手动的根文件系统：

```
# zfs mount zroot/ROOT/void
# zfs mount -a
```


在这一点上，整个 ZFS 层次结构应该被挂载并准备好安装。为了提高启动时的导入速度，在一个缓存文件中记录当前的池子配置是很有用的，Void 将使用这个文件来避免走遍整个设备层次结构来识别可导入的池子:

```
# mkdir -p /mnt/etc/zfs
# zpool set cachefile=/mnt/etc/zfs/zpool.cache zroot
```

在适当的地方挂载非 ZFS 文件系统。例如，如果 `/dev/sda2` 持有一个 ext4 文件系统，应该被挂载在`/boot`，而`/dev/sda1` 是 EFI 系统分区:

```
# mkdir -p /mnt/boot
# mount /dev/sda2 /mnt/boot
# mkdir -p /mnt/boot/efi
# mount /dev/sda1 /mnnt/boot/efi
```

### 安装

在这一点上，普通的安装可以从标准 chroot 安装指南的 ["Base Installation"
section](https://docs.voidlinux.org/installation/guides/chroot.html#base-installation) 部分进行。然而，在按照 ["Finalization" instructions](https://docs.voidlinux.org/installation/guides/chroot.html#finalization) 的说明进行安装之前，要确保已经安装了zfs包，并且dracut被配置可以识别ZFS根文件系统：

```
(chroot) # mkdir -p /etc/dracut.conf.d
(chroot) # cat > /etc/dracut.conf.d/zol.conf <<EOF
nofsck="yes"
add_dracutmodules+=" zfs "
omit_dracutmodules+=" btrfs resume "
EOF
(chroot) # xbps-install zfs
```

最后，按照 "Finalization" instructions 并重新启动进入您的新系统。

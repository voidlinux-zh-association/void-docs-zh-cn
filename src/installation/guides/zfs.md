# 全盘 ZFS

因为 Void 的安装程序不支持 ZFS，所以有必要通过 chroot 来安装。除了一些关于引导程序和 initramfs 支持的注意事项外，在 ZFS 根文件系统上安装 Void 与其他高级安装没有明显区别。[ZFSBootMenu](https://zfsbootmenu.org) 是一个从头开始设计的引导程序，支持直接从 ZFS 池中启动 Linux 发行版。然而，也可以使用传统的引导程序与ZFS根文件系统。

## ZFSBootMenu

尽管它可以启动（并且可以在上面运行）各种各样的发行版，但 ZFSBootMenu 官方认为 Void 是一等的发行版。ZFSBootMenu 支持原生的 ZFS 加密，提供方便的恢复环境，可用于克隆先前的快照或在预启动环境中执行高级操作，并支持从任何可被现代 ZFS 驱动程序导入的池中启动。ZFSBootMenu 文档除其他内容外，还提供了几个从头开始安装 Void 系统的[步骤指南](https://docs.zfsbootmenu.org/en/latest/guides/void-linux.html)。[UEFI 指南](https://docs.zfsbootmenu.org/en/latest/guides/void-linux/uefi.html)描述了现代系统引导 Void 系统的过程。对于传统的 BIOS 系统，[syslinux 指南](https://docs.zfsbootmenu.org/en/latest/guides/void-linux/syslinux-mbr.html)提供了类似的说明。


## 传统引导程序

对于那些希望放弃 ZFSBootMenu 的人来说，可以用另一个引导程序来引导 Void 系统。为了避免不必要的复杂性，使用ZFSBootMenu 以外的引导程序的系统应该计划使用单独的 `/boot`，它位于ext4或xfs文件系统中。

### 安装媒介

把 Void 安装到 ZFS 根目录下需要一个带有 ZFS 驱动的安装介质。可以从官方构建自定义镜像 [void-mklive](https://github.com/void-linux/void-mklive) 提供命令行选项 `-p zfs` 到 `mklive.sh` 脚本。对于 `x86_64` 系统来说，获取一个预先构建的 [hrmpf](https://github.com/leahneukirchen/hrmpf/releases) 镜像可能更方便。这些镜像由 Void 团队成员维护，是标准 Void Live 镜像的扩展，包括预编译的ZFS模块和其他有用的工具。

### 磁盘分区

After booting a live image with ZFS support, [partition your
disks](../live-images/partitions.md). The considerations in the partitioning
guide apply to ZFS installations as well, except that

- The boot partition should be considered necessary unless you intend to use
   `gummiboot`, which expects that your EFI system partition will be mounted at
   `/boot`. (This alternative configuration will not be discussed here.)
- Aside from any EFI system partition, GRUB BIOS boot partition, swap or boot
   partitions, the remainder of the disk should typically be a single partition
   with type code `BF00` that will be dedicated to a single ZFS pool. There is
   no benefit to creating separate ZFS pools on a single disk.

As needed, format the EFI system partition using
[mkfs.vfat(8)](https://man.voidlinux.org/mkfs.vfat.8) and the the boot partition
using [mke2fs(8)](https://man.voidlinux.org/mke2fs.8) or
[mkfs.xfs(8)](https://man.voidlinux.org/mkfs.xfs.8). Initialize any swap space
using [mkswap(8)](https://man.voidlinux.org).

> It is possible to put Linux swap space on a ZFS zvol, although there may be a
> risk of deadlocking the kernel when under high memory pressure. This guide
> takes no position on the matter of swap space on a zvol. However, if you wish
> to use suspension-to-disk (hibernation), note that the kernel is not capable
> of resuming from memory images stored on a zvol. You will need a dedicated
> swap partition to use hibernation. Apart from this caveat, there are no
> special considerations required to resume a suspended image when using a ZFS
> root.

### Create a ZFS pool

Create a ZFS pool on the partition created for it using
[zpool(8)](https://man.voidlinux.org/zpool.8). For example, to create a pool on
`/dev/disk/by-id/wwn-0x5000c500deadbeef-part3`:

```
# zpool create -f -o ashift=12 \
    -O compression=lz4 \
    -O acltype=posixacl \
    -O xattr=sa \
    -O relatime=on \
    -o autotrim=on \
    -m none zroot /dev/disk/by-id/wwn-0x5000c500deadbeef-part3
```

Adjust the pool (`-o`) and filesystem (`-O`) options as desired, and replace the
partition identifier `wwn-0x5000c500deadbeef-part3` with that of the actual
partition to be used.

> When adding disks or partitions to ZFS pools, it is generally advisable to
> refer to them by the symbolic links created in `/dev/disk/by-id` or (on UEFI
> systems) `/dev/disk/by-partuuid` so that ZFS will identify the right
> partitions even if disk naming should change at some point. Using traditional
> device nodes like `/dev/sda3` may cause intermittent import failures.

Next, export and re-import the pool with a temporary, alternate root path:

```
# zpool export zroot
# zpool import -N -R /mnt zroot
```

### Create initial filesystems

The filesystem layout on your ZFS pool is flexible. However, it is customary to
put operating system root filesystems ("boot environments") under a `ROOT`
parent:

```
# zfs create -o mountpoint=none zroot/ROOT
# zfs create -o mountpoint=/ -o canmount=noauto zroot/ROOT/void
```

Setting `canmount=noauto` on filesystems with `mountpoint=/` is useful because
it permits the creation of multiple boot environments (which may be clones of a
common Void installation or contain completely separate distributions) without
fear that ZFS auto-mounting will attempt to mount one over another.

To separate user data from the operating system, create a filesystem to store
home directories:

```
# zfs create -o mountpoint=/home zroot/home
```

Other filesystems may be created as desired.

### Mount the ZFS hierarchy

All ZFS filesystems should be mounted under the `/mnt` alternate root
established by the earlier re-import. Mount the manual-only root filesystem
before allowing ZFS to automatically mount everything else:

```
# zfs mount zroot/ROOT/void
# zfs mount -a
```

At this point, the entire ZFS hierarchy should be mounted and ready for
installation. To improve boot-time import speed, it is useful to record the
current pool configuration in a cache file that Void will use to avoid walking
the entire device hierarchy to identify importable pools:

```
# mkdir -p /mnt/etc/zfs
# zpool set cachefile=/mnt/etc/zfs/zpool.cache zroot
```

Mount non-ZFS filesystems at the appropriate places. For example, if `/dev/sda2`
holds an ext4 filesystem that should be mounted at `/boot` and `/dev/sda1` is
the EFI system partition:

```
# mkdir -p /mnt/boot
# mount /dev/sda2 /mnt/boot
# mkdir -p /mnt/boot/efi
# mount /dev/sda1 /mnnt/boot/efi
```

### Installation

At this point, ordinary installation can proceed from the ["Base Installation"
section](https://docs.voidlinux.org/installation/guides/chroot.html#base-installation).
of the standard chroot installation guide. However, before following the
["Finalization"
instructions](https://docs.voidlinux.org/installation/guides/chroot.html#finalization),
make sure that the `zfs` package has been installed and `dracut` is configured
to identify a ZFS root filesystem:

```
(chroot) # mkdir -p /etc/dracut.conf.d
(chroot) # cat > /etc/dracut.conf.d/zol.conf <<EOF
nofsck="yes"
add_dracutmodules+=" zfs "
omit_dracutmodules+=" btrfs resume "
EOF
(chroot) # xbps-install zfs
```

Finally, follow the "Finalization" instructions and reboot into your new system.

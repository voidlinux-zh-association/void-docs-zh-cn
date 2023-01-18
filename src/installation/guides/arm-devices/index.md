# ARM 设备

Void Linux 为几种 ARM 设备提供了软件包和镜像。在这些设备上安装 Void 可以通过几种方式进行：

- [预构建的镜像](预构建的镜像): 可以直接在 SD 卡或其他存储介质上刷镜像，但它给你的分区布局有限，如果你想增加分区的大小，需要手动扩展。
   
- [Tarball 安装](#tarball-安装): PLATFORMFS 和 ROOTFS tarballs，可以提取到先前准备好的分区方案。
- [Chroot 安装](#chroot-安装): 遵循 [chroot 指南](https://docs.voidlinux.org/installation/guides/chroot.html)中概述的大部分步骤。

本指南还概述了主要针对此类设备的[配置步骤](#配置)。

由于本指南中的大多数命令将在外部存储上运行，因此在移除设备之前运行 [sync(1)](https://man.voidlinux.org/sync.1) 是很重要的。

## 安装

如果你要在 "[支持的平台](./platforms.md)" 页面中所涉及的 ARM 设备上安装 Void Linux，请确保彻底阅读其部分。

### 预构建的镜像

在[下载并验证镜像](../../index.md)后，可以用 [cat(1)](https://man.voidlinux.org/cat.1) 、[pv(1)](https://man.voidlinux.org/pv.1) 或 [dd(1)](https://man.voidlinux.org/dd.1) 将其写入相关的媒体。例如，将其闪存到位于 `/dev/mmcblk0` 的SD卡上。

```
# dd if=<image>.img of=/dev/mmcblk0 bs=4M status=progress
```

### 自定义分区布置

定制安装 -- 例如，定制分区布局 -- 需要一个更复杂的过程。两个可用的选项是：

- [Tarball 安装](#tarball-安装); 和
- [Chroot 安装](#chroot-安装).

要为这些安装方法准备存储，需要对存储介质进行分区，然后将分区挂载到正确的挂载点。

ARM 设备的常用分区方案需要至少两个分区，在使用 MS-DOS 分区表格式化的驱动器上：

- 一个格式化为 `FAT32`，分区类型为 `0c`，将挂载在 `/boot`；
- one that can be formatted as any file system that Linux can boot from, such as
   ext4, which will be mounted on `/`. If you're using an SD card, you can
   create the ext4 file system with the `^has_journal` option - this disables
   journaling, which might increase the drive's life, at the cost of a higher
   chance of data loss.

There are a variety of tools available for partitioning, e.g.
[cfdisk(8)](https://man.voidlinux.org/cfdisk.8).

To access the newly created file systems, it is necessary to mount them. This
guide will assume that the second partition will be mounted on `/mnt`, but you
may mount it elsewhere. To mount these filesystems, you can use the commands
below, replacing the device names with the appropriate ones for your setup:

```
# mount /dev/mmcblk0p2 /mnt
# mkdir /mnt/boot
# mount /dev/mmcblk0p1 /mnt/boot
```

#### Tarball 安装

First, [download and verify](../../index.md#downloading-installation-media) a
PLATFORMFS or ROOTFS tarball for your desired platform and [prepare your storage
medium](#custom-partition-layout). Then, unpack the tarball onto the file system
using [tar(1)](https://man.voidlinux.org/tar.1):

```
# tar xvfp <image>.tar.xz -C /mnt
```

#### Chroot 安装

It is also possible to perform a chroot installation, which can require the
`qemu-user-static` package together with either the `binfmt-support` or `proot`
package if a computer with an incompatible architecture (such as i686) is being
used. This guide explains how to use the `qemu-<platform>-static` program from
`qemu-user-static` with [proot(1)](https://man.voidlinux.org/proot.1).

First, [prepare your storage medium](#custom-partition-layout). Then, follow
either the [XBPS chroot installation](../chroot.md#the-xbps-method) or the
[ROOTFS chroot installation](../chroot.md#the-rootfs-method) steps, using the
appropriate architecture and base packages, some of which are listed in the
"[Supported Platforms](./platforms.md)" section.

Finally, follow the [chroot configuration steps](../chroot.md#configuration)
steps, but instead of using the [chroot(1)](https://man.voidlinux.org/chroot.1)
command to [enter the chroot](../chroot.md#entering-the-chroot), use the
following command, replacing `<platform>` with `arm` for armv6l and armv7l
devices, and with `aarch64` for aarch64 devices:

```
# proot -q qemu-<platform>-static -r /mnt -w /
```

## 配置

Some additional configuration steps need to be followed to guarantee a working
system. Configuring a [graphical
session](../../../config/graphical-session/index.md) should work as normal.

### Logging in

For the pre-built images and tarball installations, the `root` user password is
`voidlinux`.

### fstab

The `/boot` partition should be added to `/etc/fstab`, with an entry similar to
the one below. It is possible to boot without that entry, but updating the
kernel package in that situation can lead to breakage, such as being unable to
find kernel modules, which are essential for functionality such as wireless
connectivity. If you aren't using an SD card, replace `/dev/mmcblk0p1` with the
appropriate device path.

```
/dev/mmcblk0p1 /boot vfat defaults 0 0
```

### 系统时间

Several of the ARM devices supported by Void Linux don't have battery powered
real time clocks (RTCs), which means they won't keep track of time once powered
off. This issue can present itself as HTTPS errors when browsing the Web or
using the package manager. It is possible to set the time manually using the
[date(1)](https://man.voidlinux.org/date.1) utility. In order to fix this issue
for subsequent boots, install and enable [an NTP
client](../../../config/date-time.md#ntp). Furthermore, it is possible to
install the `fake-hwclock` package, which provides the `fake-hwclock` service.
[fake-hwclock(8)](https://man.voidlinux.org/fake-hwclock.8) periodically stores
the current time in a configuration file and restores it at boot, leading to a
better initial approximation of the current time, even without a network
connection.

**Warning**: Images from before 2020-03-16 might have an issue where the
installation of the `chrony` package, the default NTP daemon, is incomplete, and
the system will be missing the `chrony` user. This can be checked in the output
of the [getent(1)](https://man.voidlinux.org/getent.1) command, which will be
empty if it doesn't exist:

```
$ getent group chrony
chrony:x:997
```

In order to fix this, it is necessary to reconfigure the `chrony` package using
[xbps-reconfigure(1)](https://man.voidlinux.org/xbps-reconfigure).

### 图形 session

The `xf86-video-fbturbo` package ships a modified version of the [DDX Xorg
driver](../../../config/graphical-session/xorg.md#ddx) found in the
`xf86-video-fbdev` package, which is optimized for ARM devices. This can be used
for devices which lack more specific drivers.

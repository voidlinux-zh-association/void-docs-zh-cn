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
-  一个可以被格式化为任何 Linux 可以启动的文件系统，例如 ext4，它将被挂载到 `/` 。如果你使用的是 SD 卡，你可以用 `^has_journal` 选项创建 ext4 文件系统 - 这可以禁用日志，这可能会增加硬盘的寿命，但代价是数据丢失的可能性更高。

有各种各样的工具可用于分区，比如
[cfdisk(8)](https://man.voidlinux.org/cfdisk.8).

为了访问新创建的文件系统，有必要对其进行挂载。本指南假定第二个分区将被挂载在 `/mnt` 上，但你也可以把它挂载在其他地方。要挂载这些文件系统，你可以使用下面的命令，将设备名称替换为适合你的设置的名称:

```
# mount /dev/mmcblk0p2 /mnt
# mkdir /mnt/boot
# mount /dev/mmcblk0p1 /mnt/boot
```

#### Tarball 安装

首先，为你想要的平台[下载并验证](../../index.md) PLATFORMFS 或 ROOTFS tarball，并准备好你的存储介质。然后，使用 [tar(1)](https://man.voidlinux.org/tar.1) 将 tarball 解压到文件系统中：

```
# tar xvfp <image>.tar.xz -C /mnt
```

#### Chroot 安装

也可以參考"[支持的平台](./platforms.md) "部分执行[chroot安装](./chroot.md)。

如果在不兼容架构（如 x86_64）的计算机上执行此操作，请安装 `qemu-user-static` 和 `binfmt-support` 软件包，并在安装前启用 `binfmt-support` 服务。

最后，按照 [chroot 配置步骤](../chroot.md#配置)进行操作，但不要使用 [chroot(1)](https://man.voidlinux.org/chroot.1) 命令[进入 chroot](../chroot.md#进入-chroot)，而是使用下面的命令，对于 armv6l 和 armv7l 设备，将 `<platform>` 替换为 `arm` ，对于 aarch64 设备，替换为`aarch64`。


## 配置

需要遵循一些额外的配置步骤，以保证系统的工作。配置一个[图形 session](../../../config/graphical-session/index.md)应该正常工作。


### 登录

对于预先建立的镜像和 tarball 安装，`root` 用户密码是 `voidlinux` 。

### fstab

`/boot` 分区应该被添加到 `/etc/fstab` 中，并有一个类似于下面的条目。没有这个条目也可以启动，但是在这种情况下更新内核包可能会导致故障，比如无法找到内核模块，而这些模块对于无线连接等功能是必不可少的。如果你没有使用SD卡，用适当的设备路径替换 `/dev/mmcblk0p1`。


```
/dev/mmcblk0p1 /boot vfat defaults 0 0
```

### 系统时间

Void Linux 支持的一些 ARM 设备没有电池供电的实时时钟（RTC），这意味着一旦断电，它们将无法跟踪时间。在浏览网页或使用软件包管理器时，这个问题可能表现为 HTTPS 错误。可以使用 [date(1)](https://man.voidlinux.org/date.1) 工具手动设置时间。为了在以后的启动中解决这个问题，请安装并启用一个 [NTP 客户端](../../../config/date-time.md#ntp)。此外，还可以安装 `fake-hwclock` 软件包，它提供了 `fake-hwclock` 服务。[fake-hwclock(8)](https://man.voidlinux.org/fake-hwclock.8) 定期在配置文件中存储当前时间，并在启动时恢复它，从而导致对当前时间更好的初始近似，即使没有网络连接。

**警告**: 2020-03-16 之前的镜像可能有一个问题，即默认的 NTP 守护程序 `chrony` 包的安装不完整，系统将缺少`chrony` 用户。这可以从 [getent(1)](https://man.voidlinux.org/getent.1) 命令的输出中检查出来，如果它不存在，将是空的。


```
$ getent group chrony
chrony:x:997
```

为了解决这个问题，有必要重新配置 `chrony` 包装使用 
[xbps-reconfigure(1)](https://man.voidlinux.org/xbps-reconfigure).

### 图形 session

`xf86-video-fbturbo` 软件包提供了 `xf86-video-fbdev` 软件包中的 [DDX Xorg 驱动程序](../../../config/graphical-session/xorg.md#ddx)的修改版本，该版本针对 ARM 设备进行了优化。这可以用于那些缺乏更多特定驱动程序的设备。

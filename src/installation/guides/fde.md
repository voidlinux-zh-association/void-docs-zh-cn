# 全盘加密安装

**警告**: 你的硬盘的信息和其他信息可能不同，所以要确保它是正确的。

## 分区

启动 live 镜像并登录

用 [cfdisk](https://man.voidlinux.org/cfdisk) 在磁盘上创建一个物理分区，并将其标记为 bootable 。对于一个MBR系统，分区布局应该如下。

```
# fdisk -l /dev/sda
Disk /dev/sda: 48 GiB, 51539607552 bytes, 100663296 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x4d532059

Device     Boot Start       End   Sectors Size Id Type
/dev/sda1  *     2048 100663295 100661248  48G 83 Linux
```

UEFI 系统将需要磁盘有一个 GPT 磁盘标签和一个 EFI 系统分区。EFI 系统分区方面所需的大小可能因需求而异，但 100M 对大多数情况来说应该是足够的。对于EFI系统，分区布局如下。

```
# fdisk -l /dev/sda
Disk /dev/sda: 48 GiB, 51539607552 bytes, 100663296 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: EE4F2A1A-8E7F-48CA-B3D0-BD7A01F6D8A0

Device      Start       End   Sectors  Size Type
/dev/sda1    2048    264191    262144  128M EFI System
/dev/sda2  264192 100663262 100399071 47.9G Linux filesystem
```

## 加密卷配置

[Cryptsetup](https://man.voidlinux.org/cryptsetup.8) 默认为 LUKS2 ，但 2.06 之前的 GRUB 版本只支持 LUKS1。

GRUB 只部分支持 LUKS2；具体来讲，只[实现](https://git.savannah.gnu.org/cgit/grub.git/commit/?id=365e0cc3e7e44151c14dd29514c2f870b49f9755)了 PBKDF2 的密钥推导功能,，这*不是* LUKS2 使用的默认 KDF，即 Argon2i([GRUB Bug 59409](https://savannah.gnu.org/bugs/?59409))。使用Argon2i的LUKS加密分区（以及其他KDF）不能被解密。由于这个原因，本指南只推荐使用LUKS1。

请记住，在EFI系统上，加密的卷将是 `/dev/sda2` ，因为 `/dev/sda1` 被EFI分区占用了。


```
# cryptsetup luksFormat --type luks1 /dev/sda1

WARNING!
========
This will overwrite data on /dev/sda1 irrevocably.

Are you sure? (Type uppercase yes): YES
Enter passphrase:
Verify passphrase:
```

一旦卷被创建，它就需要打开。用一个合适的名字代替 `voidvm` 。同样，在EFI系统上加密卷是 `/dev/sda2`。

```
# cryptsetup luksOpen /dev/sda1 voidvm
Enter passphrase for /dev/sda1:
```

一旦 LUKS 容器被打开，使用该分区创建 LVM 卷组。

```
# vgcreate voidvm /dev/mapper/voidvm
  Volume group "voidvm" successfully created
```

现在应该有名为 `voidvm` 的空卷组。

接下来，需要为该卷组创建逻辑卷。在这个例子中，我为 `/` 选择了 10G，为 `swap` 选择了 2G，并将其余的分配给 `/home`。

```
# lvcreate --name root -L 10G voidvm
  Logical volume "root" created.
# lvcreate --name swap -L 2G voidvm
  Logical volume "swap" created.
# lvcreate --name home -l 100%FREE voidvm
  Logical volume "home" created.
```

接下来，创建文件系统。下面的例子使用 XFS 作为作者的个人偏好。任何由 [GRUB 支持的文件系统](https://www.gnu.org/software/grub/manual/grub/grub.html#Features) 的文件系统都可以工作。

```
# mkfs.xfs -L root /dev/voidvm/root
meta-data=/dev/voidvm/root       isize=512    agcount=4, agsize=655360 blks
...
# mkfs.xfs -L home /dev/voidvm/home
meta-data=/dev/voidvm/home       isize=512    agcount=4, agsize=2359040 blks
...
# mkswap /dev/voidvm/swap
Setting up swapspace version 1, size = 2 GiB (2147479552 bytes)
```

## 安装系统

接下来，设置 chroot 并安装基本系统。

```
# mount /dev/voidvm/root /mnt
# for dir in dev proc sys run; do mkdir -p /mnt/$dir ; mount --rbind /$dir /mnt/$dir ; mount --make-rslave /mnt/$dir ; done
# mkdir -p /mnt/home
# mount /dev/voidvm/home /mnt/home
```

在 UEFI 系统中，EFI 系统分区也需要挂载。

```
# mkfs.vfat /dev/sda1
# mkdir -p /mnt/boot/efi
# mount /dev/sda1 /mnt/boot/efi
```

将 RSA 密钥从安装介质复制到目标根目录：

```
# mkdir -p /mnt/var/db/xbps/keys
# cp /var/db/xbps/keys/* /mnt/var/db/xbps/keys/
```

在我们进入 chroot 完成配置之前，我们进行实际的安装。不要忘记为你要安装的系统类型使用适当的[镜像源 URL](../../xbps/repositories/index.md)。

```
# xbps-install -Sy -R https://repo-default.voidlinux.org/current -r /mnt base-system lvm2 cryptsetup grub
[*] Updating `https://repo-default.voidlinux.org/current/x86_64-repodata' ...
x86_64-repodata: 1661KB [avg rate: 2257KB/s]
130 packages will be downloaded:
...
```

UEFI 系统的软件包选择略有不同。UEFI 系统的安装命令将如下。

```
# xbps-install -Sy -R https://repo-default.voidlinux.org/current -r /mnt base-system cryptsetup grub-x86_64-efi lvm2
```

当它完成后，我们就可以进入 `chroot` 并完成配置。

```
# chroot /mnt
# chown root:root /
# chmod 755 /
# passwd root
# echo voidvm > /etc/hostname
```

以及，仅适用于 glibc 系统的配置：

```
# echo "LANG=en_US.UTF-8" > /etc/locale.conf
# echo "en_US.UTF-8 UTF-8" >> /etc/default/libc-locales
# xbps-reconfigure -f glibc-locales
```

### 文件系统配置

下一步是编辑 `/etc/fstab`，这将取决于你如何配置和命名你的文件系统。在这个例子中，该文件是这样的：

```
# <file system>	   <dir> <type>  <options>             <dump>  <pass>
tmpfs             /tmp  tmpfs   defaults,nosuid,nodev 0       0
/dev/voidvm/root  /     xfs     defaults              0       0
/dev/voidvm/home  /home xfs     defaults              0       0
/dev/voidvm/swap  swap  swap    defaults              0       0
```

UEFI 系统也有一个 EFI 系统分区的条目。

```
/dev/sda1	/boot/efi	vfat	defaults	0	0
```

### GRUB 配置

接下来，配置 GRUB 以解锁文件系统。 添加以下行到 `/etc/default/grub`:

```
GRUB_ENABLE_CRYPTODISK=y
```

接下来，需要对内核进行配置以找到加密的设备。首先，找到该设备的 UUID。

```
# blkid -o value -s UUID /dev/sda1
135f3c06-26a0-437f-a05e-287b036440a4
```

编辑 `/etc/default/grub` 中的 `GRUB_CMDLINE_LINUX_DEFAULT=` 行，并在其中加入 `rd.lvm.vg=voidvm rd.luks.uuid=<UUID>`。确保 UUID 与上面 [blkid(8)](https://man.voidlinux.org/blkid.8)  命令的输出中发现的 `sda1` 设备的 UUID 一致。

## LUKS 密钥设置

为了避免在启动时输入两次密码，将配置一个密钥，在启动时自动解锁加密的卷。首先，生成一个随机密钥。

```
# dd bs=1 count=64 if=/dev/urandom of=/boot/volume.key
64+0 records in
64+0 records out
64 bytes copied, 0.000662757 s, 96.6 kB/s
```

接下来，将密钥添加到加密卷中。

```
# cryptsetup luksAddKey /dev/sda1 /boot/volume.key
Enter any existing passphrase:
```

改变权限以保护生成的密钥。

```
# chmod 000 /boot/volume.key
# chmod -R g-rwx,o-rwx /boot
```

这个密钥文件也需要被添加到 `/etc/crypttab`。同样，在 EFI 系统上这将是 `/dev/sda2`。

```
voidvm   /dev/sda1   /boot/volume.key   luks
```

然后需要在 initramfs 中包含密钥文件和 `crypttab`。在 `/etc/dracut.conf.d/10-crypt.conf` 创建一个新文件，内容如下：

```
install_items+=" /boot/volume.key /etc/crypttab "
```

## 完成系统安装

接下来，将引导程序安装到磁盘上。

```
# grub-install /dev/sda
```

确保生成一个 initramfs。

```
# xbps-reconfigure -fa
```

退出 `chroot`，卸载文件系统，并重新启动系统。

```
# exit
# umount -R /mnt
# reboot
```

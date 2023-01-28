# 创建和使用 chroots 和容器

chroots 和容器的设置和使用有很多目的，包括：

- 在 musl 上运行 glibc 软件（反之亦然） 
- 在一个更受控制的环境中运行软件或沙盒环境
- 创建一个用于引导系统的 rootfs

## 创建 Chroot 

### xvoidstrap

[`xvoidstrap(1)`](https://man.voidlinux.org/xvoidstrap.1) (来自 `xtools`)可以用于创建 chroot。

```
# mkdir <chroot_dir>
# XBPS_ARCH=<chroot_arch> xvoidstrap <chroot_dir> base-voidstrap <other_pkgs>
```

`<other_pkgs>` 只有在你想在 chroot 中预装其他软件包时才需要。

### 手动方式

另外，这个过程也可以手动完成。

创建一个包含 chroot 的目录，然后通过 `base-voidstrap` 包在其中安装一个基础系统。

```
# mkdir -p "<chroot_dir>/var/db/xbps/keys"
# cp -a /var/db/xbps/keys/* "<chroot_dir>/var/db/xbps/keys"
# XBPS_ARCH=<chroot_arch> xbps-install -S -r <chroot_dir> -R <repository> base-voidstrap <other_pkgs>
```

`<repository>` 可能因[架构不同而不同](../../xbps/repositories/index.md#the-main-repository)。

`<other_pkgs>` 只有在你想在 chroot 中预装其他软件包时才需要。

## Chroot 使用方法

### xchroot

[`xchroot(1)`](https://man.voidlinux.org/xchroot.1) (来自 `xtools`）可以用来自动设置和进入 chroot。

### 手动方式

另外，这个过程也可以手动完成。

如果需要网络访问，将 `/etc/resolv.conf` 复制到 chroot 中；`/etc/hosts` 可能也需要复制。

然后需要挂载几个目录，如下所示：

```
# mount -t proc none <chroot_dir>/proc
# mount -t sysfs none <chroot_dir>/sys
# mount --rbind /dev <chroot_dir>/dev
# mount --rbind /run <chroot_dir>/run
```

使用 [chroot(1)](https://man.voidlinux.org/chroot.1) 切换到新的 root，然后像往常一样运行程序和做任务。一旦完成了 chroot，使用 [umount(8)](https://man.voidlinux.org/umount.8) 卸载 chroot。如果在没有卸载的情况下对 chroot 目录进行了任何破坏性的操作，你可能需要重新启动以重新填充受影响的目录。


### 替代品

#### Bubblewrap

[bwrap(1)](https://man.voidlinux.org/bwrap.1) (来自 `bubblewrap` 软件包）有额外的功能，如沙箱能力，不需要root 权限。

`bwrap` 非常灵活，可以用在很多方面，比如说:

```
$ bwrap --bind <chroot_dir> / \
	--dev /dev \
	--proc /proc \
	--bind /sys /sys \
	--bind /run /run \
	--ro-bind /etc/resolv.conf /etc/resolv.conf \
	--ro-bind /etc/passwd /etc/passwd \
	--ro-bind /etc/group /etc/group \
	<command>
```

在这个例子中，你将不能添加或编辑用户或用户组。当用 Xorg 运行图形应用程序时，你可能还需要绑定挂载  `~/.Xauthority` 或其他文件或目录。

[bwrap(1) manpage](https://man.voidlinux.org/bwrap.1) 手册和[Arch Wiki文章](https://wiki.archlinux.org/title/Bubblewrap#Usage_examples)中包含了更多关于 `bwrap` 使用的例子


#### Flatpak

[Flatpak](../external-applications.md#flatpak) 是一个方便的选择，可以在 glibc 和 musl 系统上运行许多应用程序，包括图形或专有应用程序。

#### 应用容器

如果需要一个更加集成和完善的解决方案，Void 还提供了与 [docker](https://www.docker.com) 和 [podman](https://man.voidlinux.org/podman.1) 等[工具一起工作的OCI容器](https://github.com/void-linux/void-docker/pkgs/container/void-linux)。这些容器在使用前不需要创建一个 chroot 目录。


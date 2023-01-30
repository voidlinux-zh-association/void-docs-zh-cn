# 内核

## 内核系列

Void Linux 在默认版本库中提供了许多内核系列。这些被命名为 `linux<x>.<y>`：例如，`linux4.19`。你可以通过运行以下命令查询所有可用的内核系列：

```
$ xbps-query --regex -Rs '^linux[0-9.]+-[0-9._]+'
```

默认安装的 `linux` 元包依赖于其中一个内核包，通常是包含最新的主线内核的包，可以与所有 DKMS 模块一起工作。较新的内核可能在资源库中可用，但不一定被认为稳定到可以作为默认的内核；使用这些内核的风险由你自己承担。如果你希望使用一个较新的内核，并且有需要构建的 DKMS 模块，请安装相关的 `linux<x>.<y>-headers` 软件包，然后使用 [xbps-reconfigure(1)](https://man.voidlinux.org/xbps-reconfigure.1) 重新配置你安装的 `linux<x>.<y>` 软件包。这将构建 DKMS 模块。

## 删除旧内核

当更新内核时，旧的版本会被留下，以防有必要回滚到旧的版本。随着时间的推移，旧的内核版本会积累起来，消耗磁盘空间，增加 DKMS 模块更新的时间。此外，如果 `/boot` 是一个独立的分区，并且被旧的内核填满，更新可能会失败，或者导致生成不完整的 initramfs 文件系统，如果它们被启动，会导致内核恐慌(kernel panics)。因此，建议定时地清理旧内核。

`vkpurge` 预先安装在每个 Void Linux 系统上，删除旧内核是通过 [vkpurge(8)](https://man.voidlinux.org/vkpurge.8) 工具完成的。这个工具在移除旧内核时运行必要的 hooks。注意，`vkpurge` 不会删除内核 *软件包*，只删除特定的 *内核*。

## 移除默认的内核系列

如果你已经安装了默认之外的系列内核包，并且想移除默认的内核包，你应该安装 `linux-base` 包，或者在已经安装的情况下将其[标记为手动软件包](https://man.voidlinux.org/xbps-pkgdb.1)。在这个过程之后，你可以用 [xbps-remove(1)](https://man.voidlinux.org/xbps-remove.1) 删除默认的内核包。可能有必要在 [xbps.d(5)](https://man.voidlinux.org/xbps.d.5) 中的 `ignorepkg` 条目中加入 `linux` 和 `linux-headers`，因为基础包可能依赖于它们。

## 改变默认的 initramfs 生成器

默认情况下，Void Linux 使用 [dracut](https://man.voidlinux.org/dracut.8) 为安装的内核准备 initramfs 镜像。替代品如 [mkinitcpio](https://man.voidlinux.org/mkinitcpio.8) 是可用的。每个 initramfs 生成器都在 initramfs 组中注册了一个 [XBPS alternative](https://man.voidlinux.org/xbps-alternatives.1) ，以链接其[内核 hooks](#内核-hooks)，在为特定内核创建或移除 initramfs 镜像时使用。

要用 mkinitcpio *等等* 替换 dracut，请安装 `mkinitcpio` 软件包；确认 `mkinitcpio` 出现在可用的替代品列表中，方法是运行:

```
$ xbps-alternatives -l -g initramfs
```

执行指令

```
# xbps-alternatives -s mkinitcpio
```

用 mkinitcpio 提供的钩子替换 dracut 的内核 hooks。在随后的内核更新中（或对 DKMS 包的更新触发了 initramfs 的再生），mkinitcpio 将被用来代替 dracut 来准备 initramfs 镜像。要强迫镜像重新生成，请重新配置你的内核包，运行：

```
# xbps-reconfigure -f linux<x>.<y>
```

对于当前安装的每个 `linux<x>.<y>` 软件包。

## cmdline

内核、初始 RAM 磁盘（initrd）和一些系统程序可以在启动时通过内核命令行参数进行配置。内核理解的参数在 [kernel-parameters](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html)文档和[bootparam(7)](https://man.voidlinux.org/bootparam.7) 中解释。dracut 理解的参数可以在[dracut.cmdline(7)](https://man.voidlinux.org/dracut.cmdline.7) 中找到。

一旦系统被启动，当前的内核命令行参数可以在 `/proc/cmdline` 文件中找到。一些系统程序可以根据命令行中传递的参数来改变它们的行为，例如，在[启动不同的 runtvdir](./services/index.md#booting-a-different-runsvdir) 时就会发生这种情况。

有不同的方法来设置这些参数，下面将解释其中一些方法。

### GRUB

内核命令行参数可以通过编辑 `/etc/default/grub`，改变 `GRUB_CMDLINE_LINUX_DEFAULT` 变量，然后运行`update-grub`，通过 GRUB 引导程序添加。

### dracut

Dracut 提供了一个 [`kernel_cmdline` 配置选项](https://man.voidlinux.org/dracut.conf.5) 和[`--kernel-cmdline`命令行选项](https://man.voidlinux.org/dracut.8)，将直接在 initramfs 镜像中编码命令行参数。当 dracut 被用来创建一个 UEFI 可执行文件时，用这些选项设置的参数将被传递给内核。然而，当产生一个普通的 initramfs 时，这些选项在启动时不会被传递给内核。相反，它们将被写入镜像中的 `/etc/cmdline.d` 中的一个配置文件。虽然 dracut 解析这个配置来控制它自己的启动时行为，但内核本身不会知道通过这个机制设置的任何东西。

在修改 dracut 配置后，重新生成 initramfs，以确保它包括这些变化。

## 内核加固

Void Linux在默认时启用了一些内核安全选项。这最初是由内核命令行参数 `slub_debug=P page_poison=1` 提供的，但从内核 5.3 系列开始，这些参数被 `init_on_alloc` 和 `init_on_free` 取代（见[此提交](https://github.com/torvalds/linux/commit/6471384af)）。

Void 的内核在可用的情况下默认启用 `init_on_alloc` 选项（即 `linux5.4` 及以上版本）。在大多数情况下，你通常不应该禁用它，因为它对性能的影响相当小（1% 以内）。`init_on_free` 选项更加昂贵（平均 5% 左右），需要通过在内核命令行传递 `init_on_free=1` 来手动启用。如果你需要禁用 `init_on_alloc`，你可以通过传递 `init_on_alloc=0` 来实现。

有可能你现有的系统仍然启用了旧的选项。它们在较新的内核中仍然有效，但对性能的影响与 `init_on_free=1` 更为接近。在较老的硬件上，这可能是相当明显的。如果你运行的内核系列早于 5.4，你可以保留它们（或者增加它们），以牺牲速度为代价获得额外的安全性；否则你应该删除它们。

## 内核模块

内核模块通常是文件系统或设备驱动程序。

###  在启动过程中加载内核模块

通常情况下，内核会自动加载所需的模块，但有时可能需要明确指定启动时要加载的模块。

为了在启动过程中加载内核模块，需要创建一个像 `/etc/modules-load.d/virtio.conf` 这样的 `.conf` 文件，其内容是：

```
# load virtio-net
virtio-net
```

### 把内核模块禁用 

将内核模块禁用是一种防止模块被内核加载的方法。有两种不同的方法将内核模块禁用，一种是由 initramfs 加载的模块，另一种是 initramfs 进程结束后加载的模块。由 initramfs 加载的模块必须在 initramfs 配置中禁用。

要将 initramfs 过程后加载的模块列入黑名单，创建一个 `.conf` 文件，如 `/etc/modprobe.d/radeon.conf`，其内容为：

```
blacklist radeon
```

#### 在 initramfs 中禁用模块 

在对配置文件进行必要的修改后，需要重新生成 initramfs，以便在下一次启动时使修改生效。

##### dracut

Dracut 可以通过一个配置文件配置为不包括内核模块。要把模块列入 dracut initramfs 的黑名单，可以创建一个`.conf` 文件，如 `/etc/dracut.conf.d/radeon.conf`，其内容如下：

```
omit_drivers+=" radeon "
```

##### mkinitcpio

To blacklist modules from being included in a mkinitcpio initramfs a `.conf`
file needs to be created like `/etc/modprobe.d/radeon.conf` with the contents:

为了将模块禁用，需要创建一个 `.conf` 文件，如 `/etc/modprobe.d/radeon.conf`，以防止模块被包含在 mkinitcpio initramfs 中。

```
blacklist radeon
```

## 内核 hooks

Void Linux 在 `/etc/kernel.d/{pre-install,post-install,pre-remove,post-remove}` 中为内核 hooks 提供了目录。

这些 hooks 被用来更新像 `grub`、`gummiboot` 和 `lilo` 等引导程序的引导菜单。

### 安装 hooks

`{pre,post}-install` hooks 是由 [xbps-reconfigure(1)](https://man.voidlinux.org/xbps-reconfigure.1) 在配置 Linux 内核时执行的，比如构建其 initramfs。这发生在第一次安装或更新内核系列时，但也可以手动运行：

```
# xbps-reconfigure --force linux<x>.<y>
```

如果手动运行，它们的作用是将 initramfs 的配置变化应用于下一次启动。

### 删除 hooks

[vkpurge(8)](https://man.voidlinux.org/vkpurge.8) 在删除旧内核时执行 `{pre,post}-remove` hooks。

## 动态内核模块支持 (DKMS)

有一些不属于 Linux 源码树的内核模块，在安装时使用 DKMS 和内核 hooks 构建。可用的模块可以通过在软件包库中搜索`dkms` 来列出。

DKMS 构建日志在 `/var/lib/dkms/`.

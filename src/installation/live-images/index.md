# Live 安装程序

Void 提供的 live 安装器镜像包括一组基础工具、安装器程序和安装全新 Void 系统需要的软件包。Live 镜像可以用来修复无法引导或正常运行的系统。

`glibc` 和 `musl` 都有 `x86_64` 镜像，但只有 `glibc` 支持 `i686` 架构。Live 安装程序不支持其他架构，其他架构的用户需要用 rootfs 压缩包安装，或者手动安装。

## 安装程序镜像

Void 发布两种镜像：base 镜像和 xfce 桌面镜像，推荐 Linux 新手尝试功能更完善的 xfce 桌面镜像。高手们往往会倾向于用 base 镜像安装系统，只安装他们需要的软件包。

### base 镜像

base 镜像提供了能用来安装 Void 系统的，最小化的软件包集合。这些基础软件包仅够用来配置新机器、升级系统和从仓库里安装其他软件包。

### Xfce 镜像

xfce 桌面镜像包括完整的桌面环境、Web 浏览器和自带的基本工具。 与 base 镜像的唯一区别是它增加了软件包和服务。 

包括一下软件:

- **窗口管理器:** xfwm4
- **文件管理器:** Thunar
- **Web 浏览器:** Firefox
- **终端:** xfce4-terminal
- **文本编辑器:** Mousepad
- **图片查看器:** Ristretto
- **其他:** Bulk rename, Orage Globaltime, Orage Calendar, Task Manager, Parole
   Media Player, Audio Mixer, MIME type editor, Application finder
 
xfce 映像的安装过程与 base 映像基本相同，除了在安装时你必须选择 `Local` 作为软件源。 若你选择了 `Network` 源，安装程序将下载并安装最新版本的 base 系统，live 镜像中将不包含任何其他软件包。

## 无障碍支持

所有的Void安装程序图像都支持控制台的屏幕阅读器 [espeakup](https://man.voidlinux.org/espeakup.8) 还有控制台盲文显示驱动 [brltty](https://man.voidlinux.org/brltty.1)。这些服务可以在启动时通过按启动程序菜单中的 `s` 来启用无障碍支持。在基于 UEFI 的系统上，GRUB 是引导程序，当菜单可用时，它将播放双音鸣叫。在基于 BIOS 的系统和传统/兼容模式的 UEFI 系统上，SYSLINUX 是引导程序，不播放钟声。SYSLINUX 还需要在按下 `s` 后按下回车键。热键 `r` 也会在支持无障碍的情况下启动，但会将实时 ISO 加载到 RAM 中。

在启动到激活了无障碍支持的安装程序镜像后，如果检测到有多个声卡，一个简短的音频菜单允许选择用于屏幕阅读器的声卡。当听到所需声卡的提示音时，按回车键来选择它。

如果在安装程序中选择了 `Local` 安装源，`espeakup` 和 `brltty` 也将被安装，并且如果在 Live 环境中启用，也将在安装的系统上启用。

xfce 安装镜像也支持图形化的读屏器 `orca` 。这可以通过按 `Win + R` 并输入 `orca -r` 来启用。如果选择了 `Local` 安装源，Orca 也将在安装到系统上。

## 内核 Command-line 参数

Void 安装程序镜像支持几个内核 command-line 参数，这些参数可以改变 Live 系统的行为。参见[the void-mklive README for a full list](https://github.com/void-linux/void-mklive#kernel-command-line-parameters) 。
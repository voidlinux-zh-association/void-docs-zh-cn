# Xorg

本节详细介绍了手动安装和配置 Xorg 显示服务器以及常见的相关服务和工具。如果你只想安装一个完整的桌面环境，建议尝试 [xfce 镜像](../../installation/live-images/index.md#xfce-image)。


## 安装

Void 提供了一个全面的 `xorg` 包，它安装了服务器和所有免费的视频驱动、输入驱动、字体和基本应用程序。这个软件包是一个安全的选择，对于大多数不需要专有视频驱动程序的系统来说应该是足够的。

如果你想只选择你需要的软件包，`xorg-minimal` 软件包 *只包含* 基本的 xorg 服务器。只安装 `xorg-minimal`，你可能需要安装一个字体包（如 `xorg-fonts`），一个终端模拟器（如 `xterm`），以及一个窗口管理器，以拥有一个可用的图形系统。


## 显卡驱动

Void同时提供开源和专有（non-free）视频驱动。

### 开源驱动

Xorg 可以使用两类开源的驱动程序。DDX 或 modesetting。

#### DDX

DDX 驱动默认与 `xorg` 包一起安装，如果安装了 `xorg-minimal` 包，也可以单独安装。它们是由 `xf86-video-*` 软件包提供的。


对于高级配置，请参见与厂商名称相对应的手册页，如
 [intel(4)](https://man.voidlinux.org/intel.4).

#### Modesetting

Modesetting  需要 `mesa-dri` 软件包，而没有额外的供应商特定的驱动软件包。

如果存在 DDX 驱动，Xorg 默认为 DDX 驱动，所以在这种情况下，必须明确选择 modeetting：请看[强制设置 modesetting 驱动程序](#强制设置-modesetting-驱动程序)。

有关高级配置，请参阅 
[modesetting(4)](https://man.voidlinux.org/modesetting.4).

### 专有驱动程序

Void 还提供专有的 [NVIDIA 驱动程序](./graphics-drivers/nvidia.md)， 中可用在[非自由存储库](../../xbps/repositories/index.md#nonfree)。 

## 输入驱动

Xorg 有许多可用的输入驱动。如果安装了 `xorg-minimal`，而某一设备没有响应，或者表现得出乎意料，那么不同的驱动程序可能会纠正这一问题。这些驱动可以抓取从电源按钮到鼠标和键盘的一切。它们是由 `xf86-input-*` 软件包提供的。

## Xorg 配置

虽然 Xorg 通常会自动检测驱动程序，而且不需要配置，但特定键盘驱动程序的配置可能看起来像一个文件 `/etc/X11/xorg.conf.d/30-keyboard.conf` ，其内容是这样的:

```
Section "InputClass"
  Identifier "keyboard-all"
  Driver "evdev"
  MatchIsKeyboard "on"
EndSection
```

### 强制设置 modesetting 驱动程序

创建文件 `/etc/X11/xorg.conf.d/10-modesetting.conf` ：

```
Section "Device"
    Identifier "GPU0"
    Driver "modesetting"
EndSection
```

并重新启动Xorg。验证配置是否实践过了：

```
$ grep -m1 '(II) modeset([0-9]+):' /var/log/Xorg.0.log
```

如果有匹配的，就说明正在使用 modesetting 。

## 启动 X Sessions

### startx

`xinit` 包提供了 [startx(1)](https://man.voidlinux.org/startx.1) 脚本作为 [xinit(1)](https://man.voidlinux.org/xinit.1) 前端，它可以用来从控制台启动 X 会话。例如，要启动 [i3(1)](https://man.voidlinux.org/i3.1)，编辑 `~/.xinitrc`，在最后一行包含 `exec /bin/i3`。

要在 X 会话中同时启动任意程序，请在 `~/.xinitrc` 中的最后一行之前添加它们。例如，要在启动 i3 之前启动[pipewire(1)](https://man.voidlinux.org/pipewire.1) ，在最后一行之前添加 `pipewire &`。

下面是一个启动 `pipewire` 和 `i3` 的 `~/.xinitrc` 文件:

```
pipewire &
exec /bin/i3
```

然后调用 `startx` 来启动 session 。

如果需要一个 D-Bus 会话总线，你可以[手动启动一个](../session-management.md#d-bus)。

### 显示管理器

显示管理器（DMs）提供了一个图形化的登录界面。Void 仓库中有许多 DM，包括 `gdm`（GNOME 的 DM）、`sddm`（KDE 的 DM）和 `lightdm`。当设置一个显示管理器时，一定要在启用前[测试该服务](../services/index.md#testing-services)。


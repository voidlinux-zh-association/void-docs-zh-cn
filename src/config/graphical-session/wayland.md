# Wayland

本节详细介绍了Wayland合成器以及相关服务和工具的手动安装和配置。

## 安装

不像 [Xorg](./xorg.md), Wayland的实现将显示服务器、窗口管理器和合成器结合在一个应用程序中。

### 桌面环境

GNOME、KDE Plasma 和 Enlightenment 有 Wayland 会话。GNOME 默认使用其 Wayland 会话。当使用这些桌面环境时，用GTK+构建的应用程序会自动选择Wayland后端，而QT5和EFL应用如在KDE或Enlightenment之外使用，可能需要分别[设置环境变量](#原生应用程序) 

### 混成器

Void Linux目前打包了以下Wayland混成器:

- Weston：本身还是其它 Wayland 混成器开发的实现参考
- Sway: 兼容 i3 的 Wayland 混成器，基于 wlroots
- Wayfire 3D 混成器，受 Compiz 启发并基于 wlroots 开发
- Hikari：基于 wlroots 并受 cwm 启发开发，在 FreeBSD 上开发很活跃，但也支持 Linux
- Cage: 显示单个全屏应用程序（就像自助取款机那样）
- River：动态平铺 Wayland 混成器，受 dwm 和 bspwm 启发

### 显卡驱动

GNOME和KDE Plasma都有针对Wayland的EGLStreams后端，这意味着它们可以使用NVIDIA专有的驱动程序。其他大多数Wayland混成器需要实现GBM接口的驱动。用于这一目的的主要驱动由`mesa-dri`软件包提供。[图形化驱动](./graphics-drivers/index.md)" 一节有关于在不同系统中设置图形的更多细节。

### Seat management

Wayland混成器需要某种方式来控制视频显示和访问输入设备。在Void系统中，这需要一个seat manager服务，它可以是elogind或seatd。
启用它们将在["Session and Seat Management"](../session-management.md) 环节中解释。
### 应用

基于[Qt5](https://wayland.freedesktop.org/qt5.html)-的应用程序需要安装`qt5-wayland`包，并设置环境变量`QT_QPA_PLATFORM=wayland-egl`来启用其Wayland后端。一些KDE特定的应用程序也需要安装kwayland包。
基于[EFL](https://wayland.freedesktop.org/efl.html)-的应用程序需要设置环境变量`ELM_DISPLAY=wl`，如果不设置，可能会有问题，因为它不能正确支持XWayland。
基于[SDL](https://libsdl.org)-的应用程序需要设置环境变量 `SDL_VIDEODRIVER=wayland`。
基于[GTK+](https://wiki.gnome.org/Initiatives/Wayland/GTK%2B)-的应用程序应该会自动使用Wayland后端。关于其他工具包的信息可以在Wayland文档 [Wayland文档](https://wayland.freedesktop.org/toolkits.html)里找到。

多媒体软件,如[mpv(1)](https://man.voidlinux.org/mpv.1),
[vlc(1)](https://man.voidlinux.org/vlc.1) 和 `imv` 可以在Wayland上原生工作。

#### 网页浏览器
Mozilla Firefox带有一个Wayland后端，默认情况下是禁用的。要启用Wayland后端，可以在运行`firefox`之前设置环境变量`MOZ_ENABLE_WAYLAND=1`，或者使用提供的`firefox-wayland`脚本。

基于GTK+或Qt5的浏览器，如Midori和[qutebrowser(1)](https://man.voidlinux.org/qutebrowser.1)应该可以在Wayland上正常工作。

#### 在Wayland中运行Xorg程序

如果一个应用程序不支持Wayland，它仍然可以在Wayland环境下使用。XWayland 是一个 X 服务器，它为大多数 Wayland 混成器弥补了这一缺陷，并作为大多数混成器的依赖项被安装。它的软件包是`xorg-server-xwayland`。对于 Weston，应安装 `weston-xwayland`。

## 配置

Wayland 库需要[`XDG_RUNTIME_DIR`](../session-management.html#xdg_runtime_dir) 环境变量来确定 Wayland 套接字的目录

也有可能一些应用程序以某种方式使用 `XDG_SESSION_TYPE` 环境变量，这需要你将其设置为 `wayland`。

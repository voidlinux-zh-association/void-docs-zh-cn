# Wayland

本节详细介绍了 Wayland 合成器以及相关服务和工具的手动安装和配置。

## 安装

不像 [Xorg](./xorg.md), Wayland 的实现将显示服务器、窗口管理器和合成器结合在一个应用程序中。

### 桌面环境

GNOME、KDE Plasma 和 Enlightenment 有 Wayland 会话。GNOME 默认使用其 Wayland 会话。当使用这些桌面环境时，用 GTK+ 构建的应用程序会自动选择 Wayland 后端，而 QT5 和 EFL 应用如在 KDE 或 Enlightenment 之外使用，可能需要分别[设置环境变量](#原生应用程序) 

### 混成器

Void Linux 目前打包了以下 Wayland 混成器:

- Weston：本身还是其它 Wayland 混成器开发的实现参考
- Sway: 兼容 i3 的 Wayland 混成器，基于 wlroots
- Wayfire 3D 混成器，受 Compiz 启发并基于 wlroots 开发
- Hikari：基于 wlroots 并受 cwm 启发开发，在 FreeBSD 上开发很活跃，但也支持 Linux
- Cage: 显示单个全屏应用程序（就像自助取款机的显示界面那样）
- River：动态平铺 Wayland 混成器，受 dwm 和 bspwm 启发

### 显卡驱动

GNOME 和 KDE Plasma 都有针对 Wayland 的 EGLStreams 后端，这意味着它们可以使用 NVIDIA 专有的驱动程序。其他大多数 Wayland 混成器需要实现 GBM 接口的驱动。用于这一目的的主要驱动由 `mesa-dri` 软件包提供。[图形化驱动](./graphics-drivers/index.md)" 一节有关于在不同系统中设置图形的更多细节。

### Seat management

Wayland 混成器需要某种方式来控制视频显示和访问输入设备。在 Void 系统中，这需要一个 seat manager 服务，它可以是 elogind 或 seatd。
启用它们将在["Session 和 Seat Management"](../session-management.md) 环节中解释。

### 原生应用程序

基于 [Qt5](https://wayland.freedesktop.org/qt5.html) 的应用程序需要安装 `qt5-wayland` 包，并设置环境变量`QT_QPA_PLATFORM=wayland-egl` 来启用其 Wayland 后端。一些KDE特定的应用程序也需要安装 `kwayland` 包。
基于 [EFL](https://wayland.freedesktop.org/efl.html) 的应用程序需要设置环境变量 `ELM_DISPLAY=wl `，如果不设置，可能会有问题，因为它不能正确支持XWayland。
基于 [SDL](https://libsdl.org) 的应用程序需要设置环境变量 `SDL_VIDEODRIVER=wayland`。
基于 [GTK+](https://wiki.gnome.org/Initiatives/Wayland/GTK%2B) 的应用程序应该会自动使用Wayland后端。关于其他工具包的信息可以在  [Wayland 文档](https://wayland.freedesktop.org/toolkits.html)里找到。

多媒体软件,如 [mpv(1)](https://man.voidlinux.org/mpv.1),
[vlc(1)](https://man.voidlinux.org/vlc.1) 和 `imv` 可以在 Wayland 上原生工作。

#### 网页浏览器
Mozilla Firefox 带有一个 Wayland 后端，默认情况下是禁用的。要启用 Wayland 后端，可以在运行 `firefox` 之前设置环境变量 `MOZ_ENABLE_WAYLAND=1`，或者使用提供的 `firefox-wayland` 脚本。

基于GTK+或Qt5的浏览器，如 Midori 和 [qutebrowser(1)](https://man.voidlinux.org/qutebrowser.1) 应该可以在Wayland上正常工作。

#### 在 Wayland 中运行 Xorg 程序

如果一个应用程序不支持 Wayland，它仍然可以在 Wayland 环境下使用。XWayland 是一个 X 服务器，它为大多数 Wayland 混成器弥补了这一缺陷，并作为大多数混成器的依赖项被安装。它的软件包是 `xorg-server-xwayland`。对于 Weston，应安装 `weston-xwayland`。

## 配置 

Wayland 库需要 [`XDG_RUNTIME_DIR`](../session-management.html#xdg_runtime_dir) 环境变量来确定 Wayland 套接字的目录

也有可能一些应用程序以某种方式使用 `XDG_SESSION_TYPE` 环境变量，这需要你将其设置为 `wayland`。

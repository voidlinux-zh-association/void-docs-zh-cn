# GNOME

## 安装前

GNOME 同时支持 X 和 Wayland Session。按照 "[Wayland](./wayland.md) "或 "[Xorg](./xorg.md)" 部分来设置你喜欢的环境。

安装 `dbus` 软件包，确保 `dbus` 服务被启用，并重新启动以使变化生效。

## 安装

安装 `gnome` 包以获得一个 GNOME 面环境，其中包括基本的
GNOME 面和 GNOME 应用程序的一个子集。额外的应用程序可以通过
可通过 `gnome-apps` 包获得

一个最小的 GNOME 桌面环境可以通过安装 `gnome-core` 包来创建。然而，请注意，并不是所有的 GNOME 的功能都会存在或发挥作用。

如果你需要 [ZeroConf](http://www.zeroconf.org/) 支持，请安装 `avahi` 软件包并启用 `avahi-daemon` 服务。

## 启动 GNOME

`gdm` 软件包为 GNOME 显示管理器，提供了 gdm [服务](../services/index.md#testing-services)；在启用该服务之前，请对其进行测试。GDM 默认为通过 `mutter` 窗口管理器提供 Wayland 会话，但也可以选择 X 会话。

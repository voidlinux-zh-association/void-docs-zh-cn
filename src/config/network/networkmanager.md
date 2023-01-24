# NetworkManager

[NetworkManager(8)](https://man.voidlinux.org/NetworkManager.8) 是一个管理以太网、Wi-Fi 和移动宽带网络连接的守护程序。安装 `NetworkManager` 软件包以获得基本的 NetworkManager 实用程序。

## 启动 NetworkManager

在启用 NetworkManager 守护进程之前，请[禁用](../services/index.md)任何其他网络管理服务，如 [dhcpcd](./index.md#dhcpcd)、[wpa_supplicant](./wpa_supplicant.md) 或 `wicd`。这些服务都控制着网络接口的配置，并且会干扰 NetworkManager。


还要确保 `dbus` 服务被[启用](../services/index.md)和运行。NetworkManager 使用 `dbus` 向客户公开网络信息和控制接口，如果没有它，将无法启动。

最后，启用 `NetworkManager` 服务。

## 配置 NetworkManager

Users of NetworkManager must belong to the `network` group.

NetworkManager 的用户必须属于 `network` 用户组。

`NetworkManager` 软件包包括一个命令行工具 [nmcli(1)](https://man.voidlinux.org/nmcli.1) 和一个基于文本的用户界面 [nmtui(1)](https://man.voidlinux.org/nmtui.1) ，用于控制网络设置.

NetworkManager 还有许多其他的前端，包括用于系统托盘的 `nm-applet`，用于 KDE Plasma 的 `nm-plasma`，以及GNOME 中的一个内置网络配置工具。


## Eduroam 和 NetworkManager

Eduroam 是一项漫游服务，在大学和其他学术机构提供国际安全互联网接入。更多信息可以在[这里](https://eduroam.org/)找到。


### 依赖关系

安装 `python3-dbus` 软件包

### 安装

从[这里](https://cat.eduroam.org/)为你的机构下载正确的 eduroam_cat 安装程序，并执行它。它将提供一个用户界面，指导你完成这一过程。

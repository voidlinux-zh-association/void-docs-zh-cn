# IWD

[IWD](https://iwd.wiki.kernel.org/)（iNet Wireless Daemon）是一个 Linux 的无线守护进程，旨在取代 [WPA supplicant](./wpa_supplicant.md)。

## 安装

安装 `iwd` 软件包并启用 `dbus` 和 `iwd` 服务。 

## 用法

命令行客户端 [iwctl(1)](https://man.voidlinux.org/iwctl.1) 可以用来添加、删除和配置网络连接。命令可以作为参数传递；当运行时没有参数，它提供一个交互式会话。要列出可用的命令，可以运行 `iwctl help`，或者运行 `iwctl` 并在交互提示符下输入 `help`。

默认情况下，只有 root 用户和 `wheel` 组中的用户有权限操作 `iwctl`。

## 配置

配置选项和例子描述如下。更多信息请查阅相关手册页面和[上游文件](https://iwd.wiki.kernel.org/networkconfigurationsettings))。

### 守护进程配置

主配置文件位于 `/etc/iwd/main.conf` 中。如果它不存在，你可以创建它。它在 [iwd.config(5)](https://man.voidlinux.org/iwd.config.5) 中有详细说明。

### 网络配置

网络配置，包括例子，在 [iwd.network(5)](https://man.voidlinux.org/iwd.network.5) 中有记载。IWD 存储已知网络的信息，并从位于 `/var/lib/iwd` 的网络配置文件中读取预置网络的信息；IWD 监控该目录的变化。网络配置文件由 SSID 的编码组成，后面是`.open` 、`.psk` 或 `.8021x` ，由安全类型决定。

作为一个例子，一个 WPA2/PSK 安全网络的基本配置文件将被称为 `<ssid>.psk`，它将包含纯文本密码:

```
[Security]
Passphrase=<password>
```

## 故障排除

默认情况下，IWD 会创建和销毁它所管理的无线接口（例如 `wlan0`）。这可能会干扰 `udevd`，它可能会试图用它的持久性网络接口名称规则来重命名接口。作为这种干扰的症状，以下信息可能被打印到你的屏幕上：

```
[   39.441723] udevd[1100]: Error changing net interface name wlan0 to wlp59s0: Device or resource busy
[   39.442472] udevd[1100]: could not rename interface '3' from 'wlan0' to 'wlp59s0': Device or resource busy
```

一个简单的解决方法是在 `/etc/iwd/main.conf` 的 `[General]` 部分添加 `UseDefaultInterface=true`，以防止 IWD 以这种方式操纵网络接口。

另一种方法是禁止 `udevd` 使用持久的网络接口名称。 这可以通过在你的内核 [cmdline](../kernel.md#cmdline) 中添加 `net.ifnames=0` 或者在 `/etc/udev/rules.d/80-net-name-slot.rules` 中创建一个符号链接到 `/dev/null` 来实现，以屏蔽重命名规则。这种替代方法将影响所有网络设备的命名。


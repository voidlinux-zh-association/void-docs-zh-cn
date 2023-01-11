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

Network configuration, including examples, is documented in
[iwd.network(5)](https://man.voidlinux.org/iwd.network.5). IWD stores
information on known networks, and reads information on pre-provisioned networks
from network configuration files located in `/var/lib/iwd`; IWD monitors the
directory for changes. Network configuration filenames consist of the encoding
of the SSID followed by `.open`, `.psk`, or `.8021x` as determined by the
security type.

As an example, a basic configuration file for a WPA2/PSK secured network would
be called `<ssid>.psk`, and it would contain the plain text password:

```
[Security]
Passphrase=<password>
```

## Troubleshooting

By default, IWD will create and destroy the wireless interfaces (e.g. `wlan0`)
that it manages. This can interfere with `udevd`, which may attempt to rename
the interface using its rules for persistent network interface names. The
following messages may be printed to your screen as a symptom of this
interference:

```
[   39.441723] udevd[1100]: Error changing net interface name wlan0 to wlp59s0: Device or resource busy
[   39.442472] udevd[1100]: could not rename interface '3' from 'wlan0' to 'wlp59s0': Device or resource busy
```

A simple fix is to prevent IWD from manipulating the network interfaces in this
way by adding `UseDefaultInterface=true` to the `[General]` section of
`/etc/iwd/main.conf`.

An alternative approach is to disable the use of persistent network interface
names by `udevd`. This may be accomplished either by adding `net.ifnames=0` to
your kernel [cmdline](../kernel.md#cmdline) or by creating a symbolic link to
`/dev/null` at `/etc/udev/rules.d/80-net-name-slot.rules` to mask the renaming
rule. This alternative approach will affect the naming of all network devices.

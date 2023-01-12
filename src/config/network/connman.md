# ConnMan

[ConnMan(8)](https://man.voidlinux.org/connman.8) 是一个管理网络连接的守护程序，它被设计得很纤细，并尽可能少地使用资源。`connman` 软件包包含运行 `ConnMan` 的基本工具。

## 启动 ConnMan

要启用 ConnMan 守护进程，首先要[禁用](../services/index.md)任何其他网络管理服务，如 [dhcpcd](./index.md#dhcpcd)、[wpa_supplicant](./wpa_supplicant.md) 或 `wicd` 。这些服务都控制着网络接口的配置，并且相互干扰。

最后启用 `connmand` 服务。

## 配置 ConnMan

### ConnMan CLI

connman 软件包包括一个命令行工具，[connmanctl(1)](https://man.voidlinux.org/connmanctl.1) ，用来控制网络设置。如果你不提供任何命令，`connmanctl` 会以交互式 shell 的形式启动。

使用 `connmanctl` 交互式 shell 建立与接入点的连接可能看起来如下:

```
# connmanctl
> enable wifi
> agent on
> scan wifi
> services
> connect wifi_<uniqueid>
> exit
```

### ConnMan 前端工具

ConnMan 还有许多其他的前端，包括用于系统托盘的 `connman-ui` 、用于 GTK 的 `connman-gtk`、用于 QT 的 `cmst` 和用于基于 ncurses 的用户界面的 `connman-ncurses`。

## 防止 DNS 被 ConnMan 覆盖 

创建 `/etc/sv/connmand/conf` 内容如下:

```
OPTS="--nodnsproxy"
```

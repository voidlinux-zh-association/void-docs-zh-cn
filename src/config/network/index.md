# 网络

Void Linux 中的网络配置可以通过几种方式进行。默认安装时启用了 [dhcpcd(8)](https://man.voidlinux.org/dhcpcd.8) 服务。

## 接口名称

新版本的 [udev(7)](https://man.voidlinux.org/udev.7) 不再使用传统的 Linux 接口命名方案（eth0, eth1, wlan0, ...）。

这种行为可以通过在[内核 cmdline](https://docs.voidlinux.org/config/kernel.html#cmdline) 中加入 `net.ifnames=0` 来恢复。

## 静态配置

在启动时配置静态网络的一个简单方法是在 `/etc/rc.local` 文件中添加必要的 [ip(8)](https://man.voidlinux.org/ip.8) 命令：

```
ip link set dev eth0 up
ip addr add 192.168.1.2/24 brd + dev eth0
ip route add default via 192.168.1.1
```

## dhcpcd

要在所有接口上运行 [dhcpcd(8)](https://man.voidlinux.org/dhcpcd.8) ，请启用 `dhcpcd` 服务。

要想只在一个特定的接口上运行 `dhcpcd` ，请复制 `dhcpcd-eth0` 服务，并修改它以匹配你的接口。

```
$ ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
        link/ether ff:ff:ff:ff:ff:ff brd ff:ff:ff:f
# cp -R /etc/sv/dhcpcd-eth0 /etc/sv/dhcpcd-enp3s0
# sed -i 's/eth0/enp3s0/' /etc/sv/dhcpcd-enp3s0/run
# ln -s /etc/sv/dhcpcd-enp3s0 /var/service/
```

关于配置 dhcpcd 的更多信息，请参考 [dhcpcd.conf(5)](https://man.voidlinux.org/dhcpcd.conf.5)

## 无线网络

在使用无线网络之前，使用 [rfkill(8)](https://man.voidlinux.org/rfkill.8)  检查相关接口是否被软禁或硬禁。

Void 提供了几种连接到无线网络的方法：

- [wpa_supplicant](./wpa_supplicant.md)
- [iwd](./iwd.md)
- [NetworkManager](./networkmanager.md)
- [ConnMan](./connman.md)

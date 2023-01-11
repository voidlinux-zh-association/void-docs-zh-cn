# libvirt

[libvirt](https://libvirt.org/) 是一个虚拟化管理平台的 API 和守护程序，支持 LXC、KVM、QEMU、Bhyve、Xen、VMWare 和 Hyper-V 等虚拟化技术。

要使用 libvirt，请安装 `libvirt` 软件包，确保已安装 `dbus` 包，并[启用](../services/index.md) `dbus`、`libvirtd`、`virtlockd` 和 `virtlogd` 服务。`libvirtd` 守护程序可以在运行时通过 [virt-admin(1)](https://man.voidlinux.org/virt-admin.1) 重新配置。

`libvirt` 包为 libvirtd 提供了 [virsh(1)](https://man.voidlinux.org/virsh.1) 接口。`virsh` 是一个交互式 shell 和批处理脚本工具，用于执行管理任务，包括创建、配置和运行虚拟机，以及管理网络和存储。注意，`virsh` 通常需要以 root 身份运行，如 `virsh` man 页中所述。

> 由于 communications channels 原因，大多数 virsh 命令都需要 root 权限才能运行 用于与管理程序对话的通道。 以非 root 身份运行会有错误。

但是，如果你安装了 `polkit` 和 `dbus` 包，并且启用了 `dbus` 服务，`libvirtd` 将向添加的任何用户授予必要的权限到 `libvirt` 用户组。 

`virt-manager` 和 `virt-manager-tools` 软件包提供了 `virsh` 的一个替代方案。

有关 libvirt 的一般信息，请参阅 [the libvirt wiki](https://wiki.libvirt.org/page/Main_Page) 和 [the wiki's FAQ](https://wiki.libvirt.org/page/FAQ) 的常见问题解答 。 有关 libvirt 用法的介绍， 请参阅 [the "VM lifecycle" page](https://wiki.libvirt.org/page/VM_lifecycle)。 

# LXC

[Linux 容器项目](https://linuxcontainers.org/) 包括三个子项目: [LXC](https://linuxcontainers.org/lxc/introduction/)、[LXD](https://linuxcontainers.org/lxd/introduction/) 和[LXCFS](https://linuxcontainers.org/lxcfs/introduction/)。该项目还包括CGManager项目，该项目在最近的内核中已被弃用，而改用 CGroup 命名空间。


## 配置 LXC

安装 `lxc` 软件包.

以 `root` 身份创建和运行特权容器不需要任何配置，只需使用各种 `lxc-*` 命令，如 [lxc-create(1)](https://man.voidlinux.org/lxc-create.1), [lxc-start(1)](https://man.voidlinux.org/lxc-start.1), [lxc-attach(1)](https://man.voidlinux.org/lxc-attach.1)等。


### 创建无特权的容器

用户 ID（UID）和组 ID（GID）的范围通常是 0 到 65535。非特权容器通过将每个容器内的 UID 和 GID 范围映射到主机系统不使用的范围来增强安全性。未使用的主机范围必须 *从属于* 将运行无特权容器的用户。

下级 UID 和 GID 分别在 [subuid(5)](https://man.voidlinux.org/subuid.5) 和 [subgid(5)](https://man.voidlinux.org/subgid.5) 文件中分配。

要创建无特权的容器，首先编辑 `/etc/subuid` 和 `/etc/subgid` 来委托范围。比如说。


```
root:1000000:65536
user:2000000:65536
```

在每个冒号分隔的条目中： 

- 第一个字段是下级范围将被分配给的用户；
- 第二个字段是最小的数字 ID，定义了一个从属的范围; 和
- 第三个字段是该范围内连续 ID 的数量。

[usermod(8)](https://man.voidlinux.org/usermod.8) 程序也可以用来操作 suborinated ID。

一般来说，连续 ID 的数量应该是 65536 的整数倍；起始值并不重要，只是为了确保文件中定义的各种范围不重叠。在这个例子中，`root` 控制的 UID（或者，从 `subgid`，GID）范围是 1000000 到 1065535，包括在内；`user` 控制的 ID 范围是 2000000 到 2065535。

在创建一个容器之前，拥有该容器的用户将需要一个 [lxc.conf(5)](https://man.voidlinux.org/lxc.conf.5) 文件来指定要使用的 subuid 和 subgid 范围。对于根用户的容器，这个文件位于 `/etc/lxc/default.conf`；对于非特权用户，这个文件位于 `~/.config/lxc/default.conf`。

```
lxc.idmap = u 0 1000000 65536
lxc.idmap = g 0 1000000 65536
```

孤立的 `u` 字符表示一个 UID 映射，而孤立的 `g` 表示一个 GID 映射。第一个数字值一般应始终为 0；这表示从容器内看到的 UID 或 GID 范围的开始。第二个数值是 *从容器外看到的* 相应范围的开始，可以是 `/etc/subuid` 或 `/etc/subgid` 中委托的范围内的一个任意值。最后一个值是要映射的连续ID的数量。

请注意，尽管外部范围的起始点是任意的，但必须注意确保起始点和数字所暗示的范围的终点不会超出委托给用户的 ID 范围。

如果配置的是非 root 用户，请以 root 身份编辑 `/etc/lxc/lxc-usernet`，指定一个网络设备配额。例如，允许名为 `user` 的用户创建最多 10 个连接到 `lxcbr0` 网桥的 `veth` 设备:

```
user veth lxcbr0 10
```

用户现在可以使用 `lxc-*` 工具创建和使用无特权的容器。要创建一个简单的名为 `mycontainer` 的 Void 容器，使用类似的命令:

```
lxc-create -n mycontainer -t download -- \
	--dist voidlinux --release current --arch amd64
```

您可以用其他架构代替 `amd64`，也可以在命令末尾添加 `--variant musl` 来指定一个 musl 镜像。可用的容器列表见[LXC镜像服务器](http://images.linuxcontainers.org)。

默认情况下，系统容器的配置和挂载点存储在 `/var/lib/lxc`，而用户容器的配置和挂载点则存储在 `~/.local/share/lxc` 。这两个值都可以通过在 [lxc.system.conf(5)](https://man.voidlinux.org/lxc.system.conf.5) 文件中设置 `lxc.lxcpath` 来修改。特权用户可以在 `/etc/lxc/lxc.conf` 中定义的系统`lxc.lxcpath` 中启动非特权容器；普通用户可以在 `~/.config/lxc/lxc.conf` 中定义的个人 `lxc.lxcpath` 中启动非特权容器。

默认情况下，所有容器将共享相同的下级 UID 和 GID 地图。这是允许的，但这意味着攻击者如果在一个容器中获得了高等级的访问权限，并能以某种方式突破该容器，就会对其他容器有类似的访问权限。为了将容器相互隔离，在你创建每个容器之前，改变 `default.conf` 中的 `lxc.idmap` 范围，使其指向一个唯一的范围。试图修复用错误的地图创建的容器的权限是可能的，但很不方便。

## LXD

LXD 提供了 LXC 的 `lxc-*` 工具的替代接口。然而，它不需要上一节中描述的配置。

安装 `lxd` 软件包，并[启用](../services/index.md#enabling-services) `lxd` 服务。

LXD 用户必须属于 `lxd` 用户组。

使用 `lxc` 命令来管理实例，如[这里](https://linuxcontainers.org/lxd/getting-started-cli/#lxd-client)所述。

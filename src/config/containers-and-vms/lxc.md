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

The [usermod(8)](https://man.voidlinux.org/usermod.8) program may also be used
to manipulate suborinated IDs.

Generally, the number of consecutive IDs should be an integer multiple of 65536;
the starting value is not important, except to ensure that the various ranges
defined in the file do not overlap. In this example, `root` controls UIDs (or,
from `subgid`, GIDs) ranging from 1000000 to 1065535, inclusive; `user` controls
IDs ranging from 2000000 to 2065535.

Before creating a container, the user owning the container will need an
[lxc.conf(5)](https://man.voidlinux.org/lxc.conf.5) file specifying the subuid
and subgid range to use. For root-owned containers, this file resides at
`/etc/lxc/default.conf`; for unprivileged users, the file resides at
`~/.config/lxc/default.conf`. Mappings are described in lines of the form

```
lxc.idmap = u 0 1000000 65536
lxc.idmap = g 0 1000000 65536
```

The isolated `u` character indicates a UID mapping, while the isolated `g`
indicates a GID mapping. The first numeric value should generally always be 0;
this indicates the start of the UID or GID range *as seen from within the
container*. The second numeric value is the start of the corresponding range *as
seen from outside the container*, and may be an arbitrary value within the range
delegated in `/etc/subuid` or `/etc/subgid`. The final value is the number of
consecutive IDs to map.

Note that, although the external range start is arbitrary, care must be taken to
ensure that the end of the range implied by the start and number does not extend
beyond the range of IDs delegated to the user.

If configuring a non-root user, edit `/etc/lxc/lxc-usernet` as root to specify a
network device quota. For example, to allow the user named `user` to create up
to 10 `veth` devices connected to the `lxcbr0` bridge:

```
user veth lxcbr0 10
```

The user can now create and use unprivileged containers with the `lxc-*`
utilities. To create a simple Void container named `mycontainer`, use a command
similar to:

```
lxc-create -n mycontainer -t download -- \
	--dist voidlinux --release current --arch amd64
```

You may substitute another architecture for `amd64`, and you may specify a
`musl` image by adding `--variant musl` to the end of the command. See the [LXC
Image Server](http://images.linuxcontainers.org) for a list of available
containers.

By default, configurations and mountpoints for system containers are stored in
`/var/lib/lxc`, while configurations for user containers and mountpoints are
stored in `~/.local/share/lxc`. Both of these values can be modified by setting
`lxc.lxcpath` in the
[lxc.system.conf(5)](https://man.voidlinux.org/lxc.system.conf.5) file. The
superuser may launch unprivileged containers in the system `lxc.lxcpath` defined
in `/etc/lxc/lxc.conf`; regular users may launch unprivileged containers in the
personal `lxc.lxcpath` defined in `~/.config/lxc/lxc.conf`.

All containers will share the same subordinate UID and GID maps by default. This
is permissible, but it means that an attacker who gains elevated access within
one container, and can somehow break out of that container, will have similar
access to other containers. To isolate containers from each other, alter the
`lxc.idmap` ranges in `default.conf` to point to a unique range *before* you
create each container. Trying to fix permissions on a container created with the
wrong map is possible, but inconvenient.

## LXD

LXD provides an alternative interface to LXC's `lxc-*` utilities. However, it
does not require the configuration described in [the previous section](#lxc).

Install the `lxd` package, and [enable](../services/index.md#enabling-services)
the `lxd` service.

LXD users must belong to the `lxd` group.

Use the `lxc` command to manage instances, as described
[here](https://linuxcontainers.org/lxd/getting-started-cli/#lxd-client).

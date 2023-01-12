# 网络文件系统

## NFS

### 挂载 NFS 共享

要挂载一个 NFS 共享，首先要安装 `nfs-utils` 和 `sv-netmount` 软件包。

在挂载 NFS 共享之前，请[启用](./services/index.md#enabling-services) `statd`、`rpcbind` 和 `netmount` 服务。如果服务器支持`nfs4`，`statd` 服务就没有必要了。


在挂载 NFS 共享之前，请启用 statd、rpcbind 和 netmount 服务。如果服务器支持nfs4，statd服务就没有必要了。

挂载 NFS 共享 ：

```
# mount -t <mount_type> <host>:/path/to/sourcedir /path/to/destdir
```

`<mount_type>` 如果服务器支持，应该是 `nfs4`，否则就是 `nfs`。`<host>` 可以是服务器的主机名或IP地址。

安装选项可以在 [mount.nfs(8)](https://man.voidlinux.org/mount.nfs.8) 中找到，而卸载选项可以在 [umount.nfs(8)](https://man.voidlinux.org/umount.nfs.8) 中找到。

例如，将 `192.168.1.99` 的服务器上的 `/volume` 连接到你本地系统上现有的 `/mnt/volume` 目录：

```
# mount -t nfs 192.168.1.99:/volume /mnt/volume
```

要想在系统启动时挂载该目录，请在 [fstab(5)](https://man.voidlinux.org/fstab.5) 中添加一个条目：

```
192.168.1.99:/volume /mnt/volume nfs rw,hard 0 0
```

请参考 [nfs(5)](https://man.voidlinux.org/nfs.5) 以了解关于可用的挂载选项的信息。

### 设置服务器 (NFSv4, 禁用 Kerberos)

要运行 NFS 服务器，首先安装 `nfs-utils` 包。

编辑 `/etc/exports` 并添加共享卷:

```
/storage/foo    *.local(rw,no_subtree_check,no_root_squash)
```

这一行将 `/storage/foo` 目录导出到本地域的任何主机上，并具有读/写权限。关于 `no_subtree_check` 和 `no_root_squash` 选项的信息，以及更广泛的可用选项，请参考 [exports(5)](https://man.voidlinux.org/exports.5)。

最后[启用](./services/index.md#enabling-services)  `rpcbind`, `statd`, 和 `nfs-server` 服务。

这将启动您的 NFS 服务器。 要检查共享是否正常工作，请使用 [showmount(8)](https://man.voidlinux.org/showmount.8) 工具来检查 NFS 服务器状态： 

```
# showmount -e localhost
```

你可以使用 [nfs.conf(5)](https://man.voidlinux.org/nfs.conf.5) 来配置你的服务器

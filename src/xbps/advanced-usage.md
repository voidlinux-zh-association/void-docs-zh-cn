# 高级用法

## 降级

XBPS 允许你将一个软件包降级到一个特定的软件包版本。

### 通过 xdowngrade

最简单的降级方法是使用 `xtools` 软件包中的 `xdowngrade`，指定你想降级的软件包版本：

```
# xdowngrade /var/cache/xbps/pkg-1.0_1.xbps
```

### 通过 XBPS

XBPS 可用于降级到不再可用的软件包版本 在存储库索引中。

如果该软件包的版本以前已经安装过，它将在 `/var/cache/xbps/` 中可用。如果没有，它将需要从其他地方获得；在这个例子中，将假定软件包的版本已经被添加到 `/var/cache/xbps/`。

首先将包版本添加到本地存储库： 

```
# xbps-rindex -a /var/cache/xbps/pkg-1.0_1.xbps
```

然后降级 `xbps-install`: 

```
# xbps-install -R /var/cache/xbps/ -f pkg-1.0_1
```

为了强制降级/重新安装一个已经安装的软件包，`-f` 标志是必要的。

## 保留当前的软件包

要防止软件包在系统更新时被更新， 请使用 [xbps-pkgdb(1)](https://man.voidlinux.org/xbps-pkgdb.1)：

```
# xbps-pkgdb -m hold <package>
```

可以通过以下方式删除保留： 

```
# xbps-pkgdb -m unhold <package>
```

## 储存库锁定的软件包

如果你使用 `xbps-src` 从一个定制的模板或定制的构建选项来构建和安装一个软件包，你可能希望防止系统更新用一个非定制的版本来替换该软件包。为了确保一个软件包只从安装它的同一个仓库更新，你可以通过 [xbps-pkgdb(1)](https://man.voidlinux.org/xbps-pkgdb.1) **重新锁定**它：

```
# xbps-pkgdb -m repolock <package>
```

撤销锁定：

```
# xbps-pkgdb -m repounlock <package>
```

## 忽略软件包

有时，你可能希望删除一个由另一个软件包提供功能的软件包，但由于依赖关系的问题，你将无法这样做。例如， 你可能希望使用 [doas(1)](https://man.voidlinux.org/doas.1) 而不是 [sudo(8)](https://man.voidlinux.org/sudo.8)， 但由于 `sudo` 软件包是对 `base-system` 软件包的依赖， 因此无法删除它。要删除它，你需要*忽略* `sudo` 包。

要忽略一个软件包， 请在 [xbps.d(5)](https://man.voidlinux.org/xbps.d.5) 配置文件中添加一个适当的 ignorepkg 项。例如:

```
ignorepkg=sudo
```

然后你将能够使用 [xbps-remove(1)](https://man.voidlinux.org/xbps-remove.1) 删除 `sudo` 软件包。

## 虚拟软件包

虚拟包可以通过 [xbps.d(5)](https://man.voidlinux.org/xbps.d.5) `virtualpkg` 项来创建。任何对虚拟包的请求都会被解析为真实的包。例如，要创建一个 `linux` 虚拟包，它将被解析为 `linux5.6` 包，创建一个 `xbps.d` 配置文件，其内容如下：

```
virtualpkg=linux:linux5.6
```

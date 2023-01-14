# XBPS 软件包管理器

X Binary Package System（XBPS）是一个快速的软件包管理器，它是从头开始设计和实现。XBPS 由 Void Linux 团队管理，它的原始代码库在 https://github.com/void-linux/xbps

大多数通用包管理是使用以下命令完成的： 

- [xbps-query(1)](https://man.voidlinux.org/xbps-query.1) 搜索并显示本地安装的软件包的信息，如果与 `-R` 标志一起使用，则显示存储库中的软件包。
- [xbps-install(1)](https://man.voidlinux.org/xbps-install.1) 安装和更新软件包，并同步存储库的索引。
- [xbps-remove(1)](https://man.voidlinux.org/xbps-remove.1) 可以删除已安装的软件包， 也可以删除孤立的软件包和缓存的软件包文件。
- [xbps-reconfigure(1)](https://man.voidlinux.org/xbps-reconfigure.1) 运行已安装包的配置，可用于重新配置更改配置文件后的某些软件包。 后者通常需要 `--force` 标志。 
- [xbps-alternatives(1)](https://man.voidlinux.org/xbps-alternatives.1) 列出或设置已安装软件包所提供的替代品。替代品允许多个软件包通过其他冲突的应用提供共同的功能。，否则会有冲突的软件包，它通过从共同的路径创建符号链接到包的特定版本，以建立符号链接，由用户选择。
- [xbps-pkgdb(1)](https://man.voidlinux.org/xbps-pkgdb.1) 可以报告和修复包数据库中的问题，并对其进行修改。
- [xbps-rindex(1)](https://man.voidlinux.org/xbps-rindex.1) 管理本地的二进制包库。

大多数问题可以通过查阅[xbps.d(5)](https://man.voidlinux.org/xbps.d.5) 的man page来找到解决方法。

学习如何从源代码构建软件包，参阅[the README for thevoid-packages repository](https://github.com/void-linux/void-packages/blob/master/README.md).

## 更新系统

和大多数系统一样，保持 Void 版本最新十分重要。使用
[xbps-install(1)](https://man.voidlinux.org/xbps-install.1) 更新系统：

```
# xbps-install -Su
```

XBPS必须使用一个单独的事务来更新自己。如果你的更新包括 `xbps` 包，你需要第二次运行上述命令来应用其余的更新。

### 重启服务

当服务被更新时，XBPS不会重新启动服务。这项任务留给了用户自己，所以他们可以安排时间，确保备份，并且通常在更新时，用户应该在电脑前进行维护。

要找到运行与磁盘上不同版本的进程，请使用 `xtools` 包提供的 `xcheckrestart` 工具：

```
$ xcheckrestart
11339 /opt/google/chrome/chrome (deleted) (google-chrome)
```
`xcheckrestart` 将输出PID、可执行文件的路径、被启动的路径的状态（几乎都是 `deleted` ）和进程名称。
被启动的路径（几乎总是 `deleted` ）和进程名称。
`xcheckrestart`可以而且应该以非特权用户的身份运行。

### 更新后的内核崩溃问题

如果你在更新后出现了内核崩溃，很可能是你的系统在 `/boot` 中的空间用完了。请参考 "[Removing old kernels](../config/kernel.md#removing-old-kernels)" 以获得更多信息。

## 寻找文件和软件包

查找可用软件包，使用 [xbps-query(1)](https://man.voidlinux.org/xbps-query.1) :

```
$ xbps-query -Rs <search_pattern>
```

`-R` 标志指定了应搜索的存储库。如果没有它， `-s` 会搜索本地安装的软件包。

如果你在安装一个软件包后找不到你期望找到的文件或程序包，你可以使用 [xbps-query(1)](https://man.voidlinux.org/xbps-query.1) 列出该软件包所提供的文件。

```
$ xbps-query -f <package_name>
```

`xtools` 这一软件包包含了 [xlocate(1)](https://man.voidlinux.org/xlocate.1) 工具、 `xlocate` 的工作原理与
 [locate(1)](https://man.voidlinux.org/locate.1) ，但对于Void软件源中的文件：

```
$ xlocate -S
Fetching objects: 11688, done.
From https://repo-default.voidlinux.org/xlocate/xlocate
 + e122c3634...a2659176f master     -> master  (forced update)
$ xlocate xlocate
xtools-0.59_1   /usr/bin/xlocate
xtools-0.59_1   /usr/share/man/man1/xlocate.1 -> /usr/share/man/man1/xtools.1
```

也可以使用 [xbps-query(1)](https://man.voidlinux.org/xbps-query.1) 来查找文件，尽管
这是很不可取的。

```
$ xbps-query -Ro /usr/bin/xlocate
xtools-0.46_1: /usr/bin/xlocate (regular file)
```

这需要 `xbps-query` 下载每个软件包的部分内容来寻找文件。 `xlocate` 则是查询本地缓存的所有文件的索引，所以不需要网络，所以不需要网络访问。

要获得所有已安装软件包的列表，不包括其版本，运行：

```
$ xbps-query -l | awk '{ print $2 }' | xargs -n1 xbps-uhelper getpkgname
```

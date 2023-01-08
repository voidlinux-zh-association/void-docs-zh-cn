# XBPS 软件包管理

X Binary Package System（XBPS）是一个快速的软件包管理器，它是从头开始设计和实现。XBPS 由 Void Linux 团队管理，在 https://github.com/void-linux/xbps

大多数通用包管理是使用以下命令完成的： 

- [xbps-query(1)](https://man.voidlinux.org/xbps-query.1) 搜索并显示本地安装的软件包的信息，如果与 `-R` 标志一起使用，则显示存储库中的软件包。
- [xbps-install(1)](https://man.voidlinux.org/xbps-install.1) 安装和更新软件包，并同步存储库的索引。
- [xbps-remove(1)](https://man.voidlinux.org/xbps-remove.1) 可以删除已安装的软件包， 也可以删除孤立的软件包和缓存的软件包文件。
- [xbps-reconfigure(1)](https://man.voidlinux.org/xbps-reconfigure.1) 运行已安装包的配置，可用于重新配置更改配置文件后的某些软件包。 后者通常需要 `--force` 标志。 
- [xbps-alternatives(1)](https://man.voidlinux.org/xbps-alternatives.1) lists or
   sets the alternatives provided by installed packages. Alternatives is a
   system which allows multiple packages to provide common functionality through
   otherwise conflicting files, by creating symlinks from the common paths to
   package-specific versions that are selected by the user.
- [xbps-pkgdb(1)](https://man.voidlinux.org/xbps-pkgdb.1) can report and fix
   issues in the package database, as well as modify it.
- [xbps-rindex(1)](https://man.voidlinux.org/xbps-rindex.1) manages local binary
   package repositories.

Most questions can be answered by consulting the man pages for these tools,
together with the [xbps.d(5)](https://man.voidlinux.org/xbps.d.5) man page.

To learn how to build packages from source, refer to [the README for the
void-packages
repository](https://github.com/void-linux/void-packages/blob/master/README.md).

## Updating

Like any other system, it is important to keep Void up-to-date. Use
[xbps-install(1)](https://man.voidlinux.org/xbps-install.1) to update:

```
# xbps-install -Su
```

XBPS must use a separate transaction to update itself. If your update includes
the `xbps` package, you will need to run the above command a second time to
apply the rest of the updates.

### Restarting Services

XBPS does not restart services when they are updated. This task is left to the
administrator, so they can orchestrate maintenance windows, ensure reasonable
backup capacity, and generally be present for service upgrades.

To find processes running different versions than are present on disk, use the
`xcheckrestart` tool provided by the `xtools` package:

```
$ xcheckrestart
11339 /opt/google/chrome/chrome (deleted) (google-chrome)
```

`xcheckrestart` will print out the PID, path to the executable, status of the
path that was launched (almost always `deleted`) and the process name.

`xcheckrestart` can and should be run as an unprivileged user.

### Kernel Panic After Update

If you get a kernel panic after an update, it is likely your system ran out of
space in `/boot`. Refer to "[Removing old
kernels](../config/kernel.md#removing-old-kernels)" for further information.

## Finding Files and Packages

To search available repositories for packages, use
[xbps-query(1)](https://man.voidlinux.org/xbps-query.1):

```
$ xbps-query -Rs <search_pattern>
```

The `-R` flag specifies that repositories should be searched. Without it, `-s`
searches for locally-installed packages.

If you can't find a file or program you expected to find after installing a
package, you can use [xbps-query(1)](https://man.voidlinux.org/xbps-query.1) to
list the files provided by that package:

```
$ xbps-query -f <package_name>
```

The `xtools` package contains the
[xlocate(1)](https://man.voidlinux.org/xlocate.1) utility. `xlocate` works like
[locate(1)](https://man.voidlinux.org/locate.1), but for files in the Void
package repositories:

```
$ xlocate -S
Fetching objects: 11688, done.
From https://repo-default.voidlinux.org/xlocate/xlocate
 + e122c3634...a2659176f master     -> master  (forced update)
$ xlocate xlocate
xtools-0.59_1   /usr/bin/xlocate
xtools-0.59_1   /usr/share/man/man1/xlocate.1 -> /usr/share/man/man1/xtools.1
```

It is also possible to use
[xbps-query(1)](https://man.voidlinux.org/xbps-query.1) to find files, though
this is strongly discouraged:

```
$ xbps-query -Ro /usr/bin/xlocate
xtools-0.46_1: /usr/bin/xlocate (regular file)
```

This requires `xbps-query` to download parts of every package to find the file.
`xlocate`, however, queries a locally cached index of all files, so no network
access is required.

To get a list of all installed packages, without their version:

```
$ xbps-query -l | awk '{ print $2 }' | xargs -n1 xbps-uhelper getpkgname
```

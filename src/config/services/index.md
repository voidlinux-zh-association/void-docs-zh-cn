# 服务和守护进程 - runit

Void 使用 [runit(8)](https://man.voidlinux.org/runit.8) 监督组件来运行系统服务和守护程序。

使用 runit 的一些优势包括：

- 一个小的基础代码，可以更容易地审计错误和安全问题。 
- 每个服务都被赋予一个干净的进程状态，无论该服务是如何启动或重启：它将以相同的环境、资源限制、开放的文件描述符和控制终端启动。
- 可靠的服务日志记录工具，只要相关的服务在运行并写入日志，日志服务就会保持运行。

如果你不需要一个程序持续运行，但希望它定期运行，你可以考虑使用 [cron daemon](../cron.md)

## 章节内容

- [每个用户的服务](./user-services.md)
- [日志](./logging.md)

## 服务目录

每个由 runit 管理的服务都有一个相关的*服务目录*。

一个服务目录只需要一个文件：一个名为 `run` 的可执行文件，这是希望在前台执行一个进程。

可选的，一个服务目录可以包含：

- 一个名为 `check` 的可执行文件，它将被运行以检查服务是否启动和可用；如果 `check` 退出时为0，则认为它是可用的。
- 一个名为 `finish` 的可执行文件，它将在关机/进程停止时运行。
- 一个 `conf` 文件；它可以包含环境变量，并在 `run` 被引用。
- 一个名为的目录 `log`; pipe 将从 `run` 服务目录中的进程到输入的 `run` 过程中 `log` 目录。 

When a new service is created, a `supervise` folder will be automatically
created on the first run.

当一个新的服务被创建时，在第一次运行时将会自动创建一个 `supervise` 文件夹。

### 配置服务

Most services can take configuration options set by a `conf` file in the service
directory. This allows service customization without modifying the service
directory provided by the relevant package.

Check the service file for how to pass configuration parameters. A few services
have a field like `OPTS="--value ..."` in their `conf` file.

To make more complex customizations, you should [edit the
service](#editing-services).

### Editing Services

To edit a service, first copy its service directory to a different directory
name. Otherwise, [xbps-install(1)](https://man.voidlinux.org/xbps-install.1) can
overwrite the service directory. Then, edit the new service file as needed.
Finally, the old service should be stopped and disabled, and the new one should
be started.

## Managing Services

### Runsvdirs

A **runsvdir** is a directory in `/etc/runit/runsvdir` containing enabled
services in the form of symlinks to service directories. On a running system,
the current runsvdir is accessible via the `/var/service` symlink.

The `runit-void` package comes with two runsvdirs, `single` and `default`:

- `single` just runs [sulogin(8)](https://man.voidlinux.org/sulogin.8) and the
   necessary steps to rescue your system.
- `default` is the default runsvdir on a running system, unless [specified
   otherwise by the kernel command line](#booting-a-different-runsvdir).

Additional runsvdirs can be created in `/etc/runit/runsvdir/`.

See [runsvdir(8)](https://man.voidlinux.org/runsvdir.8) and
[runsvchdir(8)](https://man.voidlinux.org/runsvchdir.8) for further information.

#### Booting A Different runsvdir

To boot a runsvdir other than `default`, the name of the desired runsvdir can be
added to the [kernel command-line](../kernel.md#cmdline). As an example, adding
`single` to the kernel command line will boot the `single` runsvdir.

### Basic Usage

To start, stop, restart or get the status of a service:

```
# sv up <services>
# sv down <services>
# sv restart <services>
# sv status <services>
```

The `<services>` placeholder can be:

- Service names (service directory names) inside the `/var/service/` directory.
- The full paths to the services.

For example, the following commands show the status of a specific service and of
all enabled services:

```
# sv status dhcpcd
# sv status /var/service/*
```

See [sv(8)](https://man.voidlinux.org/sv.8) for further information.

#### Enabling Services

Void Linux provides service directories for most daemons in `/etc/sv/`.

To enable a service on a booted system, create a symlink to the service
directory in `/var/service/`:

```
# ln -s /etc/sv/<service> /var/service/
```

If the system is not currently running, the service can be linked directly into
the `default` [runsvdir](#runsvdirs):

```
# ln -s /etc/sv/<service> /etc/runit/runsvdir/default/
```

This will automatically start the service. Once a service is linked it will
always start on boot and restart if it stops, unless administratively downed.

To prevent a service from starting at boot while allowing runit to manage it,
create a file named `down` in its service directory:

```
# touch /etc/sv/<service>/down
```

The `down` file mechanism also makes it possible to disable services that are
enabled by default, such as the [agetty(8)](https://man.voidlinux.org/agetty.8)
services for ttys 1 to 6. This way, package updates which affect these services
(in this case, the `runit-void` package) won't re-enable them.

#### Disabling Services

To disable a service, remove the symlink from the running runsvdir:

```
# rm /var/service/<service>
```

Or, for example, from the `default` runsvdir, if either the specific runsvdir,
or the system, is not currently running:

```
# rm /etc/runit/runsvdir/default/<service>
```

#### Testing Services

To check if a service is working correctly when started by the service
supervisor, run it once before fully enabling it:

```
# touch /etc/sv/<service>/down
# ln -s /etc/sv/<service> /var/service/
# sv once <service>
```

If everything works, remove the `down` file to enable the service.

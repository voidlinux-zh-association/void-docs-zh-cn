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

当一个新的服务被创建时，在第一次运行时将会自动创建一个 `supervise` 文件夹。

### 配置服务

大多数服务可以接受由服务目录中的 `conf` 文件设置的配置选项。这允许在不修改相关软件包提供的服务目录的情况下对服务进行定制。

检查服务文件以了解如何传递配置参数。少数服务在其 `conf` 文件中有一个 `OPTS="--value ..."` 这样的字段。

要进行更复杂的定制，你应该[编辑该服务](#编辑服务)。

### 编辑服务

要编辑一个服务，首先要将其服务目录复制到一个不同的目录名下。否则， [xbps-install(1)](https://man.voidlinux.org/xbps-install.1) 会覆盖服务目录。然后，根据需要编辑新的服务文件。最后，旧的服务应该被停止和禁用，而新的服务应该被启动。

## 管理服务

### Runsvdirs

**runsvdir** 是 `/etc/runit/runsvdir` 中的一个目录，它包含了以服务目录符号链接形式出现的启用的服务。在一个运行中的系统中，当前的 runsvdir 可以通过 `/var/service` 符号链接访问。

`runit-void` 软件包带有两个 `runtvdirs` ，`single`和 `default`:

- `single` 只是运行 [sulogin(8)](https://man.voidlinux.org/sulogin.8)和 the
   necessary steps to rescue your system. 
   
- `default` 是运行系统的默认 runsvdir，除非[由内核命令行指定
   (#引导不同的 runsvdir)。

可以在 `/etc/runit/runsvdir/` 中创建额外的 runsvdirs.

参见 [runsvdir(8)](https://man.voidlinux.org/runsvdir.8) 和 [runsvchdir(8)](https://man.voidlinux.org/runsvchdir.8) 以获得更多信息。 

#### 引导不同的 runsvdir

要启动 `default` 以外的 runsvdir，可以在[内核 comand-line]((../kernel.md#cmdline))中加入所需的 runsvdir 的名称。作为一个例子，在内核命令行中加入 `single` 将启动 `single runsvdir`。

### 基本用法

启动、停止、重启和获取一个服务的状态。

```
# sv up <services>
# sv down <services>
# sv restart <services>
# sv status <services>
```

`<services>` 占位符可以是:

- `/var/service/` 目录内的服务名称（服务目录名称）。
- 服务的完整路径。

例如，下面的命令显示了一个特定服务和所有启用的服务的状态:

```
# sv status dhcpcd
# sv status /var/service/*
```

有关详细信息，请参阅 [sv(8)](https://man.voidlinux.org/sv.8) 

#### 启用服务

Void Linux 在 `/etc/sv/` 中为大多数守护程序提供了服务目录。

要在一个已启动的系统上启用一个服务，在 `/var/service/` 中创建一个服务目录的符号链接:

```
# ln -s /etc/sv/<service> /var/service/
```

如果系统目前没有运行，服务可以直接链接到 `default` 的 [runsvdir](#runsvdirs):

```
# ln -s /etc/sv/<service> /etc/runit/runsvdir/default/
```

这将自动启动该服务。一旦一个服务被链接，它将总是在启动时启动，并在停止时重新启动，除非被管理员关闭。

为了防止一个服务在启动时启动，同时允许 runit 管理它，在其服务目录下创建一个名为 `down` 的文件:

```
# touch /etc/sv/<service>/down
```

`down` 文件机制也使得禁用默认启用的服务成为可能，例如用于 ttys 1 到 6 的 [agetty(8)](https://man.voidlinux.org/agetty.8) 服务。这样，影响这些服务的软件包更新（在这种情况下，`runit-void` 软件包）就不会重新启用它们。


#### 禁用服务

要禁用一个服务，从正在运行的 runsvdir 中删除符号链接:

```
# rm /var/service/<service>
```

或者，例如，从 `default` 的runsvdir，如果特定的 runsvdir 或系统目前没有运行:

```
# rm /etc/runit/runsvdir/default/<service>
```

#### 测试服务

要检查一个服务在被服务监督员启动时是否正常工作，在完全启用它之前运行一次:

```
# touch /etc/sv/<service>/down
# ln -s /etc/sv/<service> /var/service/
# sv once <service>
```

如果一切正常，删除 `down` 文件以启用服务。

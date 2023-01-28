# Session 和 Seat 管理

Session 和 Seat 管理不是每个设置都需要的，但它可以用来安全地创建临时的运行时目录，提供对硬件设备和 multi-seat 功能的访问，并控制系统关闭。


## D-Bus

D-Bus 是一种 IPC（进程间通信）机制，由 Linux 中的用户空间软件使用。D-Bus 可以提供一个 system bus 和/或 session bus ，后者是针对用户 session 的。

- 要提供 system bus,你应该[启用](./services/index.md) `dbus` 服务。这可能需要重新启动系统才能正常工作。 
- 要提供 session bus，你可以用 [dbus-run-session(1)](https://man.voidlinux.org/dbus-run-session.1) 启动一个指定的程序（通常是一个窗口管理器或交互式外壳）。大多数桌面环境，如果通过一个适当的显示管理器启动，将自己启动一个 D-Bus session。

请注意，有些软件假定存在 system bus，也有其他软件则假定存在 session bus。

## elogind

[elogind(8)](https://man.voidlinux.org/elogind.8) 管理用户登录和系统电源，是 `systemd-logind` 的独立版本。elogind 为大多数桌面环境和 Wayland 合成器提供必要的功能。它也可以成为不用 root 的 [Xorg](./graphical-session/xorg.md)的机制之一。

请阅读 "[电源管理](./power-management.md)" 部分，了解安装 elogind 前需要考虑的事项。

为了使用它的功能，请安装 `elogind` 包并确保 [系统 D-Bus](#d-bus)被启用。你可能需要退出并重新登录.

如果你在使用 elogind 时有任何问题，请[启用](./services/index.md)其服务，因为等待 D-Bus 的激活会导致问题。

有一种替代的 D-Bus 配置，它利用 elogind 的功能，如 seat 检测。它需要安装 `dbus-elogind`、`dbus-elogind-libs` 和 `dbus-elogind-x11` 软件包。

## seatd

[seatd(1)](https://man.voidlinux.org/seatd.1)  是一个最小的 seat 管理守护程序，是 elogind 的替代品，主要用于 [wlroots 合成器](./graphical-session/wayland.md#standalone-compositors)。

要使用它，请安装 `seatd` 软件包并启用其服务。如果你想让非 root 用户能够访问 seatd 会话，请将他们加入 `_seatd` 组。

请注意，与 elogind 不同，seatd 除了管理 seats 之外，不做任何事情。


## XDG_RUNTIME_DIR

`XDG_RUNTIME_DIR` 是一个由 [XDG 基本目录规范定义](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html)的环境变量。它的值设置了基本目录的路径，程序应该在这个目录下存储用户特定的运行时文件。

安装 [elogind](#elogind) 作为你的 session 管理器来自动设置 `XDG_RUNTIME_DIR`。

或者，通过 shell 手动设置环境变量。请确保创建一个专门的用户目录，并将其权限设置为 `700`。一个好的默认位置是 `/run/user/$（id -u）`。

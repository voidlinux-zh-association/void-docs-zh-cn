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

To make use of its features, install the `elogind` package and make sure the
[system D-Bus](#d-bus) is enabled. You might need to log out and in again.

If you're having any issues with elogind, [enable](./services/index.md) its
service, as waiting for a D-Bus activation can lead to issues.

There is an alternative D-Bus configuration which takes advantage of elogind for
features such as seat detection. It requires installing the `dbus-elogind`,
`dbus-elogind-libs` and `dbus-elogind-x11` packages.

## seatd

[seatd(1)](https://man.voidlinux.org/seatd.1) is a minimal seat management
daemon and an alternative to elogind primarily for [wlroots
compositors](./graphical-session/wayland.md#standalone-compositors).

To use it, install the `seatd` package and enable its service. If you want
non-root users to be able to access the seatd session, add them to the `_seatd`
group.

Note that, unlike elogind, seatd doesn't do anything besides managing seats.

## XDG_RUNTIME_DIR

`XDG_RUNTIME_DIR` is an environment variable defined by the [XDG Base Directory
Specification](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html).
Its value sets the path to the base directory where programs should store
user-specific runtime files.

Install [elogind](#elogind) as your session manager to automatically set up
`XDG_RUNTIME_DIR`.

Alternatively, manually set the environment variable through the shell. Make
sure to create a dedicated user directory and set its permissions to `700`. A
good default location is `/run/user/$(id -u)`.

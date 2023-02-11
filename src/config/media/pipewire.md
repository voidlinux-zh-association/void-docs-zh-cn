# PipeWire

要使用 PipeWire，请安装 `pipewire` 软件包。

[pipewire(1)](https://man.voidlinux.org/pipewire.1) 应以用户身份启动。在你的 session 中用终端模拟器运行 pipewire 命令。

```
$ pipewire
```

当 pipewire 按预期工作时，使用你的桌面环境或 [startx](../graphical-session/xorg.md#startx) 的自动启动机制。`pipewire` 软件包提供了 `pipewire` 和`pipewire-pulse` 系统服务，但在典型的设置中不推荐使用。

`pipewire` 软件包在 `/usr/share/applications` 中为 `pipewire` 和 `pipewire-pulse` 提供桌面入口文件。如果你的环境支持桌面应用[自动启动规范](https://specifications.freedesktop.org/autostart-spec/autostart-spec-latest.html)，你可以通过符号链接桌面文件到自动启动目录来启用 `pipewire`：

```
# ln -s /usr/share/applications/pipewire.desktop /etc/xdg/autostart/pipewire.desktop
```

## Session 管理

在 PipeWire 中，会话管理器承担了媒体源的互连以及执行路由策略的责任。没有 Session 管理器，PipeWire 将无法运行。参考 [`pipewire-media-session`](https://gitlab.freedesktop.org/pipewire/media-session) 最初在 Void `pipewire` 包中提供，并配置为默认运行以满足这一要求。然而，`pipewire-media-session` 已被废弃，作者建议使用 [WirePlumber](https://pipewire.pages.freedesktop.org/wireplumber/) 代替它。安装 `wireplumber` 软件包，以便在 PipeWire 中使用这个 session 管理器。

标准的 Void 配置会使 `pipewire` 自动启动 `pipewire-media-session`，必须重写以使用 `wireplumber`。唯一需要改变的是，在 `context.exec` 部分注释掉 `pipewire-media-session` 的调用，这可以通过一个 `sed` 的替换来完成。为了使所有用户都能看到这一配置变化，将新的配置文件放在 `/etc/pipewire` 中：

```
# mkdir -p /etc/pipewire
# sed '/path.*=.*pipewire-media-session/s/{/#{/' \
    /usr/share/pipewire/pipewire.conf > /etc/pipewire/pipewire.conf
```

或者，将新的配置文件放置在单个用户的预期位置：

```
$ : "${XDG_CONFIG_HOME:=${HOME}/.config}"
$ mkdir -p "${XDG_CONFIG_HOME}/pipewire"
$ sed '/path.*=.*pipewire-media-session/s/{/#{/' \
    /usr/share/pipewire/pipewire.conf > "${XDG_CONFIG_HOME}/pipewire/pipewire.conf"
```

> 在 `/etc/pipewire` 或 `${XDG_CONFIG_HOME}/pipewire` 中的自定义 `pipewire.conf` 将完全阻止使用默认的系统配置。鼓励覆盖默认配置以启用 `wireplumber` 的用户监控默认配置，并在每次 `pipewire` 更新时核对任何变化。

现在，配置 `wireplumber`，使之与 `pipewire` 同时启动。如果你的窗口管理器或桌面环境的自动启动机制是用来启动`pipewire` 的，建议使用同样的机制来启动 `wireplumber`。`wireplumber` 软件包包括一个`wireplumber.desktop` Desktop Entry 文件，在这种情况下可能有用。

> 请注意，`wireplumber` 必须在 `pipewire` 可执行文件之后启动。桌面应用程序自动启动规范没有规定通过桌面入口文件启动的服务的顺序。当依靠这些文件来启动 `pipewire` 和 `wireplumber` 时，请查阅你的窗口管理器或桌面环境的文档，以确定是否可以实现服务的正确排序。

如果对独立的 `pipewire` 和 `wireplumber` 服务进行适当的排序是不可行的，可以配置 `pipewire` 来直接启动会话管理器。这可以通过运行:

```
# mkdir -p /etc/pipewire/pipewire.conf.d
# echo 'context.exec = [ { path = "/usr/bin/wireplumber" args = "" } ]' \
    > /etc/pipewire/pipewire.conf.d/10-wireplumber.conf
```

系统配置，或对于每个用户的配置，运行

```
$ mkdir -p "${XDG_CONFIG_HOME}/pipewire/pipewire.conf.d
$ echo 'context.exec = [ { path = "/usr/bin/wireplumber" args = "" } ]' \
    > "${XDG_CONFIG_HOME}/pipewire/pipewire.conf.d/10-wireplumber.conf"
```

在这些配置中，启动 `pipewire` 应该足以建立一个使用 `wireplumber` 进行会话管理的工作的 PipeWire session。

在其默认配置中，WirePlumber 需要一个活跃的 D-Bus 会话。如果你的桌面环境或窗口管理器被配置为提供 D-Bus 会话以及启动 `pipewire` 和 `wireplumber`，则不需要进一步配置。希望单独启动 `pipewire` 的用户，例如在`.xinitrc` 脚本中，可能会发现有必要配置 `pipewire` 来直接启动 `wireplumber`，并将 `pipewire` 的调用包装为

```
dbus-run-session pipewire
```

## PulseAudio 替代品

在启动 `pipewire-pulse` 之前，确保 PulseAudio 服务被[禁用](../services/index.md#disabling-services)，并且没有其他 PulseAudio 服务实例在运行。

通过在终端仿真器中运行 `pipewire-pulse` 来启动 PulseAudio 服务器。

为了检查替换是否正常工作，使用 [pactl(1)](https://man.voidlinux.org/pactl.1)（由 `pulseaudio-utils` 包提供）。

```
$ pactl info

[...]
Server Name: PulseAudio (on PipeWire 0.3.18)
[...]
```

一旦你确认 `pipewire-pulse` 如预期般工作，建议在启动 PipeWire 的地方自动启动它。可以修改 [pipewire.conf(5)](https://man.voidlinux.org/pipewire.conf.5) 来自动启动 PulseAudio 服务器，但不建议保持 PipeWire 配置文件不被修改，以便将来更顺利地升级。

## 蓝牙音频

为了使蓝牙音频工作，请安装 `libspa-Bluetooth` 软件包。

## ALSA 集成

安装 `alsa-pipewire` ，然后启用 PipeWire ALSA 设备并使其成为默认设备:

```
# mkdir -p /etc/alsa/conf.d
# ln -s /usr/share/alsa/alsa.conf.d/50-pipewire.conf /etc/alsa/conf.d
# ln -s /usr/share/alsa/alsa.conf.d/99-pipewire-default.conf /etc/alsa/conf.d
```

## JACK replacement

安装`libjack-pipewire`.

使用 [pw-jack(1)](https://man.voidlinux.org/pw-jack.1) 来手动启动 JACK 客户端:

```
$ pw-jack <application>
```

或者，覆盖 `libjack` 提供的库（见 [ld.so(8)](https://man.voidlinux.org/ld.so.8)）。下面的方法将在基于glibc 的系统上工作:

```
# echo "/usr/lib/pipewire-0.3/jack" > /etc/ld.so.conf.d/pipewire-jack.conf
# ldconfig
```

## 故障

替换 Pulseaudio 需要 [`XDG_RUNTIME_DIR`](../session-management.html#xdg_runtime_dir) 环境变量才能正确工作。


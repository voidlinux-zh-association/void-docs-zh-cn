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

